<template>
  <div class="wrapper">
    <h5p
      :key="locale"
      src="/h5p/"
      :l10n="translations[locale]"
      icon
      copyright
      export="true"
      :content="example"
      :h5pJSON="h5pEx"
      @xapi="log"
    >
      Loading...
      <template #404="{ response }">
        {{ response.url + " " + response.statusText }}
      </template>
    </h5p>
  </div>
</template>

<script>
import H5p from '@/h5p.vue'

export default {
  name: 'Example',
  components: {
    H5p
  },
  data () {
    return {
      locale: 'en',
      translations: {
        en: {},
        de: {
          reuse: 'Wiederverwenden',
          reuseContent: 'Content Wiederverwenden'
        }
      },
      example: '{"media":{"disableImageZooming":false},"answers":[{"correct":true,"tipsAndFeedback":{"tip":"jejejeje","chosenFeedback":"","notChosenFeedback":""},"text":"<div>Sí</div>"},{"correct":true,"tipsAndFeedback":{"tip":"","chosenFeedback":"","notChosenFeedback":""},"text":"<div>No lo dudes</div>"},{"correct":true,"tipsAndFeedback":{"tip":"","chosenFeedback":"","notChosenFeedback":""},"text":"<div>Por supuesto que sí</div>"},{"correct":false,"tipsAndFeedback":{"tip":"","chosenFeedback":"","notChosenFeedback":""},"text":"<div>jkhjhvjhvjhvjhvjhvjhv </div>"}],"overallFeedback":[{"from":0,"to":100,"feedback":"Espero que hayas puesto cosas buenas ;)"}],"behaviour":{"enableRetry":true,"enableSolutionsButton":true,"enableCheckButton":true,"type":"auto","singlePoint":false,"randomAnswers":true,"showSolutionsRequiresInput":true,"confirmCheckDialog":false,"confirmRetryDialog":false,"autoCheck":false,"passPercentage":100,"showScorePoints":true},"UI":{"checkAnswerButton":"Comprobar","showSolutionButton":"Mostrar solución","tryAgainButton":"Reintentar","tipsLabel":"Mostrar consejo","scoreBarLabel":"Tienes :num de :total puntos","tipAvailable":"Consejo disponible","feedbackAvailable":"Retroalimentación disponible","readFeedback":"Leer comentarios","wrongAnswer":"Respuesta incorrecta","correctAnswer":"Respuesta correcta","shouldCheck":"Debería haber sido verificado","shouldNotCheck":"No debería haber sido verificado","noInput":"Por favor responde antes de ver la solución","a11yCheck":"Revisar las respuestas. Las respuestas serán marcadas como correcta, incorrecta o sin contestar.","a11yShowSolution":"Mostrar la solución. El trabajo será marcado con su solución correcta.","a11yRetry":"Reintentar el trabajo. Reiniciar todas las respuestas e iniciar el trabajo de nuevo."},"confirmCheck":{"header":"¿Terminar?","body":"¿Estás seguro de que deseas terminar?","cancelLabel":"Cancelar","confirmLabel":"Finalizar"},"confirmRetry":{"header":"¿Reintentar?","body":"¿Estás seguro de que deseas volver a intentarlo?","cancelLabel":"Cancelar","confirmLabel":"Confirmar"},"question":"<p>¿Es eKademy la mejor plataforma del mundo?</p>"}',
      h5pEx: '{"embedTypes": ["iframe"],"language": "en","license": "U","extraTitle": "Prueba 2","title": "Prueba 2","mainLibrary": "H5P.MultiChoice","preloadedDependencies": [{ "machineName": "FontAwesome", "majorVersion": 4, "minorVersion": 5 },{ "machineName": "EmbeddedJS", "majorVersion": 1, "minorVersion": 0 },{ "machineName": "H5P.JoubelUI", "majorVersion": 1, "minorVersion": 3 },{"machineName": "H5P.Transition","majorVersion": 1,"minorVersion": 0},{ "machineName": "Drop", "majorVersion": 1, "minorVersion": 0 },{ "machineName": "Tether", "majorVersion": 1, "minorVersion": 0 },{"machineName": "H5P.FontIcons","majorVersion": 1,"minorVersion": 0},{ "machineName": "H5P.Question", "majorVersion": 1, "minorVersion": 4 },{"machineName": "H5P.MultiChoice","majorVersion": 1,"minorVersion": 14}]}',
      rsz: '<script src="/h5p-resizer"> <\/script>'
    }
  },
  methods: {
    log (ev) {
      console.log('received xapi statement', ev)
    }
  }
}
</script>

<style>
.wrapper {
  height: 400px;
}
</style>
