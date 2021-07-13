<template>
  <div class="wrapper">
    <input
      v-model="locale"
      type="radio"
      value="en"
    >EN
    <input
      v-model="locale"
      type="radio"
      value="de"
    >DE
    <h5p
      :key="locale"
      src="/h5p/"
      :l10n="translations[locale]"
      icon
      copyright
      export="true"
      @xapi="log"
    >
      Loading...
      <template #404="{ response }">
        {{ response.url + ' ' + response.statusText }}
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
      contentExample: '{"media":{"disableImageZooming":false},"answers":[{"correct":true,"tipsAndFeedback":{"tip":"jejejeje","chosenFeedback":"","notChosenFeedback":""},"text":"<div>Sí</div>\n"},{"correct":true,"tipsAndFeedback":{"tip":"","chosenFeedback":"","notChosenFeedback":""},"text":"<div>No lo dudes</div>\n"},{"correct":true,"tipsAndFeedback":{"tip":"","chosenFeedback":"","notChosenFeedback":""},"text":"<div>Por supuesto que sí</div>\n"},{"correct":false,"tipsAndFeedback":{"tip":"","chosenFeedback":"","notChosenFeedback":""},"text":"<div>No </div>\n"}],"overallFeedback":[{"from":0,"to":100,"feedback":"Espero que hayas puesto cosas buenas ;)"}],"behaviour":{"enableRetry":true,"enableSolutionsButton":true,"enableCheckButton":true,"type":"auto","singlePoint":false,"randomAnswers":true,"showSolutionsRequiresInput":true,"confirmCheckDialog":false,"confirmRetryDialog":false,"autoCheck":false,"passPercentage":100,"showScorePoints":true},"UI":{"checkAnswerButton":"Comprobar","showSolutionButton":"Mostrar solución","tryAgainButton":"Reintentar","tipsLabel":"Mostrar consejo","scoreBarLabel":"Tienes :num de :total puntos","tipAvailable":"Consejo disponible","feedbackAvailable":"Retroalimentación disponible","readFeedback":"Leer comentarios","wrongAnswer":"Respuesta incorrecta","correctAnswer":"Respuesta correcta","shouldCheck":"Debería haber sido verificado","shouldNotCheck":"No debería haber sido verificado","noInput":"Por favor responde antes de ver la solución","a11yCheck":"Revisar las respuestas. Las respuestas serán marcadas como correcta, incorrecta o sin contestar.","a11yShowSolution":"Mostrar la solución. El trabajo será marcado con su solución correcta.","a11yRetry":"Reintentar el trabajo. Reiniciar todas las respuestas e iniciar el trabajo de nuevo."},"confirmCheck":{"header":"¿Terminar?","body":"¿Estás seguro de que deseas terminar?","cancelLabel":"Cancelar","confirmLabel":"Finalizar"},"confirmRetry":{"header":"¿Reintentar?","body":"¿Estás seguro de que deseas volver a intentarlo?","cancelLabel":"Cancelar","confirmLabel":"Confirmar"},"question":"<p>¿Es eKademy la mejor plataforma del mundo?</p>\n"}'
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
  height: 98vh;
}
</style>
