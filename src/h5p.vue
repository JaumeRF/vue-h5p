<template>
  <div v-if="loading">
    <slot />
  </div>
  <div v-else-if="error">
    <slot
      name="error"
      :error="error"
    />
  </div>
  <iframe
    v-else
    ref="iframe"
    :srcdoc="srcdoc"
    style="width:100%;"
    @load="addEventHandlers"
  />
</template>

<script>
import Toposort from 'toposort-class'
import { FetchError } from '@/errors'
import l10n from '@/l10n'
import frameScript from 'frame/script'
import frameStyle from 'frame/style'

export default {
  name: 'H5p',
  props: {
    externalStyle: {
      type: String,
      default: ''
    },
    content: {
      type: String,
      required: true
    },
    h5pJSON: {
      type: String,
      required: true
    },
    src: {
      type: String,
      required: true
    },
    embed: {
      type: String,
      default: ''
    },
    resize: {
      type: String,
      default: ''
    },
    export: {
      type: String,
      default: ''
    },
    copyright: {
      type: Boolean,
      default: false
    },
    icon: {
      type: Boolean,
      default: false
    },
    fullscreen: {
      type: Boolean,
      default: false
    },
    l10n: {
      type: Object,
      default: () => ({})
    }
  },
  data () {
    return {
      loading: true,
      error: undefined,
      srcdoc: ''
    }
  },
  computed: {
    path () {
      return this.src.endsWith('/') ? this.src.slice(0, -1) : this.src
    }
  },
  async mounted () {
    let h5p
    let libraries
    try {
      h5p = await JSON.parse(this.h5pJSON)
      libraries = await this.loadDependencies(h5p.preloadedDependencies)
    } catch (e) {
      this.error = e
      this.loading = false
      return
    }

    const { machineName, majorVersion, minorVersion } = h5p.preloadedDependencies.find(dep => dep.machineName === h5p.mainLibrary)
    const h5pIntegration = {
      l10n: {
        H5P: Object.assign({}, l10n.H5P, this.l10n)
      },
      url: this.path,
      contents: {
        'cid-default': {
          embedCode: this.embed,
          resizeCode: this.resize,
          exportUrl: this.export,
          fullScreen: this.fullscreen,
          library: `${machineName} ${majorVersion}.${minorVersion}`,
          jsonContent: this.content,
          url: this.path,
          displayOptions: {
            frame: Boolean(this.export || this.embed || this.copyright || this.icon),
            export: Boolean(this.export),
            embed: Boolean(this.embed),
            copyright: this.copyright,
            icon: this.icon
          }
        }
      },
      _libraryPaths: Object.fromEntries(
        Object.entries(libraries).map(
          ([id, lib]) => [id, lib.path]
        )
      )
    }

    const { styles, scripts } = this.sortDependencies(libraries)

    // workaround for vue-loader parsing this as the end of our SFC's script block
    const endScript = '</' + 'script>'
    const contentStyles = styles.map(style => `<link rel="stylesheet" href="${style}">`).join('\n')
    const contentScripts = scripts.map(script => `<script src="${script}">${endScript}`).join('\n')
    this.srcdoc = `<!doctype html>
<html class="h5p-iframe" >
  <head>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Karla:ital,wght@0,200;0,300;0,400;0,500;0,600;0,700;1,200;1,300;1,400;1,500;1,600;1,700&display=swap" rel="stylesheet">
    <base target="_parent">
    <style>${frameStyle}</style>
    <link rel="stylesheet" href="${this.externalStyle}">
    ${contentStyles}
    <script>H5PIntegration = ${JSON.stringify(h5pIntegration)};var H5P = H5P || {};H5P.externalEmbed = true;${endScript}
    <script>${frameScript}${endScript}
    ${contentScripts}
  </head>
  <body>
    <div class="h5p-content" style="font-family: 'Karla', sans-serif !important;" data-content-id="default"/>
  </body>
</html>`

    this.loading = false
  },
  methods: {
    addEventHandlers () {
      try {
        this.$refs.iframe.contentWindow.H5P.externalDispatcher.on('*', (ev) => {
          this.$emit(ev.type.toLowerCase(), ev.data)
        })
      } catch (error) {
        console.log(error)
      }
    },
    async getJSON (...url) {
      const resp = await fetch(this.path + '/' + url.join('/'), { credentials: 'include' })
      if (!resp.ok) {
        let body = {}
        try {
          body = await resp.json()
        } catch { /* eslint-disable-line no-empty */ }
        throw new FetchError(resp, body)
      }
      return resp.json()
    },
    async loadDependencies (deps, libraryMap = {}) {
      await Promise.all(deps.map(async ({ machineName, majorVersion, minorVersion }) => {
        const id = `${machineName}-${majorVersion}.${minorVersion}`
        if (libraryMap[id]) return
        try {
          libraryMap[id] = {
            library: await this.getJSON(id, 'library.json'),
            path: id
          }
        } catch {
          libraryMap[id] = {
            library: await this.getJSON(machineName, 'library.json'),
            path: machineName
          }
        }
        const libDeps = libraryMap[id].library.preloadedDependencies
        if (libDeps) {
          this.loadDependencies(libDeps, libraryMap)
          libraryMap[id].dependencies = libDeps.map(({ machineName, majorVersion, minorVersion }) => `${machineName}-${majorVersion}.${minorVersion}`)
        }
      }))
      return libraryMap
    },
    sortDependencies (libraries) {
      const sorter = new Toposort()
      Object.entries(libraries)
        .forEach(([id, { dependencies = [] }]) => sorter.add(id, dependencies))
      const sorted = sorter.sort().reverse()

      const styles = sorted.map(id => libraries[id])
        .map(({ path, library }) => library.preloadedCss?.map(file => `${this.path}/${path}/${file.path}`))
        .flat(1)
        .filter(Boolean)

      const scripts = sorted.map(id => libraries[id])
        .map(({ path, library }) => library.preloadedJs?.map(file => `${this.path}/${path}/${file.path}`))
        .flat(1)
        .filter(Boolean)

      return { styles, scripts }
    }
  }
}
</script>
