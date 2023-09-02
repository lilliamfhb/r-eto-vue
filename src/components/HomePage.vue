<template>
  <v-container>
    <h1>{{formulario.name}}</h1>
    <p>{{formulario.descripcion}}</p>
    <v-form ref="form" @submit.prevent="submit()" class="c-form">
      <ul class="c-form__list ps-0">
        <li 
          v-for="(item,i) of formulario.data"
          :key="`key${i}`"
          class="c-item">
          <div class="c-item__label">
            Sección {{i+1}} de {{formulario.data.length}}
          </div>

          <div class="c-item__content">
            <h3 class="c-item__title">{{item.question}}</h3>
            <div v-if="item.type === 'SELECT'">
              <v-select
                v-model="formulario.answers[i]"
                :items="item.items"
                item-text="value"
                label="Selecciona una opción"
                :rules="rules.requerido"
              ></v-select>
            </div>
            <div v-if="item.type === 'INPUT'">
              <v-text-field
                v-model="formulario.answers[i]"
                label="Respuesta"
                :rules="rules.requerido"
              ></v-text-field>
            </div>

            <div v-if="item.type === 'CHECKBOX'">
              <v-radio-group v-model="formulario.answers[i]" :rules="rules.requerido">
                <v-radio
                v-for="(option,j) of item.items"
                :key="`option${j}`"
                :label="option"
                :value="option"></v-radio>
              </v-radio-group>
            </div>
          </div>
        </li>
      </ul>
      <v-btn type="submit" class="c-button mt-2 bg-blue-darken-1">Confirmar</v-btn>
    </v-form>

    <div class="c-answers" v-if="showAnswers">
      <h2 class="c-title">Respuestas</h2>
      <ol>
        <li 
          v-for="(item,i) of formulario.data"
          :key="`key${i}`">
          <h3>{{item.question}}</h3>
          <p><strong>
            {{formulario.answers[i]}}
            </strong></p>
        </li>
      </ol>
    </div>
  </v-container>
</template>

<script>
  export default {
    name: 'HomePage',

    data: () => ({
      showAnswers: false,

      valid: false,
      rules: {
        requerido: [
        value => {
          if (value) return true
            return 'Campo es requerido'
          },
        ]
      },

      formulario: {
        name: '',
        descripcion: '',
        data: [],
        loading: false,
        answers: [],
      }

    }),
    mounted() {
      this.getData();
    },
    methods: {
      getData() {
        fetch(`https://run.mocky.io/v3/6bd01347-72e9-49da-809a-d5002ca63b2c`, {
              method: 'GET',
            })
              .then((res) => {
                if (res.status === 201 || res.status < 300) {
                  return res.json();
                }
                throw res;
              })
              .then((response) => {
                console.log(response);
                this.formulario.name = response.formulario.name;
                this.formulario.descripcion = response.formulario.descripcion;
                this.formulario.data = response.formulario.form;
              })
              .catch((err) => {
                console.log( 'error del catch');
                throw err;
              });
      },
      submit() {
        if (this.$refs.form.validate()) {
          console.log(this.$refs.form.validate(), this.$refs.form.value, 'kkkk');
          this.showAnswers = true;
        } else {
          alert('Revise los datos e intente otra vez');
        }

      }
    }
  }
</script>
