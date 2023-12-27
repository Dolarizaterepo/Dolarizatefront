<!-- ComprobanteTransferencia.vue -->

<template>
    <v-dialog v-model="showComprobante" max-width="600">
      <v-card>
        <div class="dolarizate-topbar" style="text-align: center;">
        <div class="dolarizate-title">
          <img src="img/logo-dolarizate-blanco.svg" style="max-width: 100%;" />
        </div>
      </div>
        <v-card-title>Tu transferencia se realizó con éxito!</v-card-title>
        <v-card-text>
          <!-- Contenido del comprobante -->
          <div>
            <b>Fecha:</b> {{ obtenerFechaActual() }}
          </div>
          <div>
          <b>Transaction ID:</b> {{ transactionId }}
        </div>
          <div>
            <b>Destinatario:</b> {{ destinatario }}
          </div>
          <div>
            <b>Nombre del Destinatario:</b> {{ recipientName }}
          </div>
          <div>
            <b>Cuenta del Destinatario:</b> {{ recipientAccount }}
          </div>
          <div>
            <b>Monto Transferido:</b> ${{ valorTransferido }}
          </div>
          <!-- Agrega más detalles según sea necesario -->
        </v-card-text>
        <v-card-actions>
          <v-btn @click="descargarComprobante">Descargar</v-btn>
          <v-btn @click="showComprobante = false">Cerrar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
</template>
<script>
// Importa FileSaver.js
import { saveAs } from 'file-saver'
// Importa jsPDF
import jsPDF from 'jspdf'
export default {
  props: {
    valorTransferido: Number,
    destinatario: String
  },
  data () {
    return {
      showComprobante: true
    }
  },
  methods: {
    obtenerFechaActual () {
      const now = new Date()
      return now.toLocaleString()
    },
    descargarComprobante () {
      console.log('Destinatario:', this.$props.destinatario)
      // Crear instancia de jsPDF
      const pdf = new jsPDF()

      // Agregar contenido al PDF con estilos adicionales
      pdf.setFontSize(16)
      pdf.text(`Fecha: ${this.obtenerFechaActual()}`, 10, 10)
      pdf.text('Destinatario:', 10, 20)
      pdf.text(`Monto Transferido: $${this.valorTransferido}`, 10, 30)

      // Línea decorativa
      pdf.setLineWidth(0.5)
      pdf.setDrawColor(0, 0, 0)
      pdf.line(10, 40, 200, 40)

      // Texto adicional
      pdf.setFontSize(15)
      pdf.text('¡Gracias por confiar en nosotros!', 10, 50)

      // Guardar el PDF como blob
      const blob = pdf.output('blob')

      // Utilizar FileSaver.js para descargar el archivo
      saveAs(blob, 'comprobante_transferencia.pdf')
    }
  }
}
</script>
