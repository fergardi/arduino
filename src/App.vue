<template lang="pug">
  fieldset
    legend Datos
    fieldset
      legend Formulario
      el-form(ref="formulario", :model="formulario", :inline="true", size="mini")
        el-form-item(label="Fichero a leer:")
          input#archivo(placeholder="Archivo" type="file", accept=".csv")
        el-form-item(label="Temperatura máxima a mostrar:")
          el-input-number(placeholder="Temperatura (Cº)" v-model="formulario.temperatura")
        el-form-item
          el-button(type="primary", @click="leer") Cargar
    fieldset
      legend Tabla
      el-table(:data="filtrado", :border="true", :stripe="true", size="mini", :empty-text="vacio")
        el-table-column(prop="temperatura", label="Temperatura (Cº)", :sortable="true")
        el-table-column(prop="fecha", label="Fecha y hora", :sortable="true")
        el-table-column(prop="humedad", label="Humedad (%)", :sortable="true")
</template>

<script>
  import csv from 'csv-parse'

  export default {
    data () {
      return {
        formulario: {
          archivo: 'datos.csv',
          temperatura: 35
        },
        datos: [],
        vacio: 'No hay datos para mostrar, compruebe que el fichero existe e inténtelo de nuevo'
      }
    },
    methods: {
      leer () {
        const archivo = document.getElementById('archivo').files[0]
        const lector = new FileReader()
        lector.onload = () => {
          csv(lector.result, { columns: true, trim: true }, (error, filas) => {
            if (!error) {
              this.datos = filas
              this.$notify.success({
                title: 'Fichero aceptado',
                message: 'Formato CSV apropiado.'
              })
            } else {
              this.$notify.error({
                title: 'Fichero rechazado',
                message: 'Formato CSV inapropiado.'
              })
            }
          })
        }
        lector.onerror = () => {
          this.$notify.error({
            title: 'Error',
            message: 'Error de lectura/escritura.'
          })
        }
        lector.readAsText(archivo)
      },
      reiniciar () {
        this.formulario = {
          archivo: 'datos.csv',
          temperatura: 35
        }
        this.$refs.formulario.reset()
      }
    },
    computed: {
      filtrado () {
        return this.datos.filter((fila) => {
          return parseInt(fila.temperatura) < this.formulario.temperatura
        })
      }
    }
  }
</script>

<style lang="stylus" scoped>
  fieldset
    border 1px solid lightgrey
    border-radius 4px
    padding 20px
    margin 20px
</style>
