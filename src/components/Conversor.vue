<template>
  <div class="conversor">
    <div class="checks">
      <label class="label">
        Moeda
        <input type="checkbox" v-model="moeda" />
        <span class="checkmark"></span>
      </label>

      <label class="label">
        Maiúsculas
        <input type="checkbox" v-model="maiusculas" />
        <span class="checkmark"></span>
      </label>
    </div>

    <input
      ref="input"
      type="text"
      placeholder="Digite o número"
      v-model.lazy="numero"
      v-money="money"
    />
    <input type="hidden" v-model="numeroPorExtenso" id="extenso" />

    <span>{{ numeroPorExtenso }}</span>

    <button @click="copiar()">COPIAR</button>
  </div>
</template>

<script>
import { VMoney } from 'v-money'

export default {
  name: 'Conversor',

  directives: { money: VMoney },

  mounted() {
    this.$refs['input'].focus();
  },

  data() {
    return {
      numero: '',
      moeda: true,
      maiusculas: false,
      copyType: 'hidden',
    };
  },

  computed: {
    money() {
      return {
        decimal: this.moeda ? ',' : '',
        thousands: '.',
        prefix: this.moeda ? 'R$ ' : '',
        suffix: '',
        precision: this.moeda ? 2 : 0,
        masked: false,
      };
    },
    numeroPorExtenso() {
      const decimais = ['zero', 'um', 'dois', 'três', 'quatro', 'cinco', 'seis', 'sete', 'oito', 'nove', 'dez', 'onze', 'doze', 'treze', 'quatorze', 'quinze', 'dezesseis', 'dezessete', 'dezoito', 'dezenove'];
      const dezenas = ['dez', 'vinte', 'trinta', 'quarenta', 'cinquenta', 'sessenta', 'setenta', 'oitenta', 'noventa'];
      const centenas = ['cem', 'cento', 'duzentos', 'trezentos', 'quatrocentos', 'quinhentos', 'seiscentos', 'setecentos', 'oitocentos', 'novecentos'];
      const milhares = ['mil', 'milhão', 'bilhão', 'trilhão', 'quadrilhão', 'quintilhão', 'sextilhão', 'setilhão', 'octilhão', 'nonilhão', 'decilhão', 'undecilhão', 'dodecilhão', 'tredecilhão', 'quatrodecilhão', 'quindecilhão', 'sedecilhão', 'septendecilhão', 'octencilhão', 'nonencilhão'];
      const extensos = [decimais, dezenas, centenas, milhares];

      let arrayNumeros;
      let v;
      let i;
      const numero = this.numero.replace(this.moeda ? /[^,\d]/g : /\D/g, '').split(',');
      const e = ' e ';
      let reais = 'real';
      let centavos = 'centavo';

      for (var tamanhoNumero = numero.length - 1, l, posicaoNumero = -1, resultado = [], s = [], t = ''; ++posicaoNumero <= tamanhoNumero; s = []) {
        posicaoNumero && (numero[posicaoNumero] = (('.' + numero[posicaoNumero]) * 1).toFixed(2).slice(2));

        if (!(arrayNumeros = (v = numero[posicaoNumero]).slice((l = v.length) % 3).match(/\d{3}/g), v = l % 3 ? [v.slice(0, l % 3)] : [], v = arrayNumeros ? v.concat(arrayNumeros) : v).length) continue;

        for (arrayNumeros = -1, l = v.length; ++arrayNumeros < l; t = '') {
          if(!(i = v[arrayNumeros] * 1)) continue;
          i % 100 < 20 && (t += extensos[0][i % 100]) ||
          i % 100 + 1 && (t += extensos[1][(i % 100 / 10 >> 0) - 1] + (i % 10 ? e + extensos[0][i % 10] : ''));
          s.push((i < 100 ? t : !(i % 100) ? extensos[2][i == 100 ? 0 : i / 100 >> 0] : (extensos[2][i / 100 >> 0] + e + t)) +
          ((t = l - arrayNumeros - 2) > -1 ? ' ' + (i > 1 && t > 0 ? extensos[3][t].replace('ão', 'ões') : extensos[3][t]) : ''));
        }

        arrayNumeros = (s.length > 1 ? (arrayNumeros = s.pop(), s.join(' ') + e + arrayNumeros) : s.join('') || ((!posicaoNumero && (numero[posicaoNumero + 1] * 1 > 0) || resultado.length) ? '' : extensos[0][0]));
        arrayNumeros && resultado.push(arrayNumeros + (this.moeda ? (' ' + (v.join('') * 1 > 1 ? posicaoNumero ? centavos + 's' : (/0{6,}reais/.test(numero[0]) ? 'de ' : '') + reais.replace('l', 'is') : posicaoNumero ? centavos : reais)) : ''));
      }

      const resultadoFinal = resultado.join(e);
      return this.maiusculas ? resultadoFinal.toUpperCase() : resultadoFinal;
    },
  },

  methods: {
    copiar() {
      const copyText = document.querySelector("#extenso");
      copyText.type = 'text';
      copyText.select();
      document.execCommand("copy");
      window.getSelection().removeAllRanges();
      copyText.type = 'hidden';
    },
  },
};
</script>

<style scoped>
.conversor {
  width: 100%;
  height: 100vh;
  background-color: #dadada;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

input {
  height: 65px;
  border-radius: 5px;
  text-align: center;
  border: 0;
  margin-bottom: 30px;
  font-size: 3.5rem;
  background-color: #dadada;
  color: #727272;
}

span {
  font-size: 2rem;
  text-align: center;
  width: 80%;
  margin-bottom: 30px;
}

button {
  height: 35px;
}

.checks {
  display: flex;
  justify-content: space-around;
  width: 22%;
  margin-bottom:30px;
}

/* The container */
.label {
  display: block;
  position: relative;
  padding-left: 35px;
  margin-bottom: 12px;
  cursor: pointer;
  font-size: 22px;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

/* Hide the browser's default checkbox */
.label input {
  position: absolute;
  opacity: 0;
  cursor: pointer;
  height: 0;
  width: 0;
}

/* Create a custom checkbox */
.checkmark {
  position: absolute;
  top: 0;
  left: 0;
  height: 25px;
  width: 25px;
  background-color: #eee;
}

/* On mouse-over, add a grey background color */
.label:hover input ~ .checkmark {
  background-color: #ccc;
}

/* When the checkbox is checked, add a blue background */
.label input:checked ~ .checkmark {
  background-color: #2196F3;
}

/* Create the checkmark/indicator (hidden when not checked) */
.checkmark:after {
  content: "";
  position: absolute;
  display: none;
}

/* Show the checkmark when checked */
.label input:checked ~ .checkmark:after {
  display: block;
}

/* Style the checkmark/indicator */
.label .checkmark:after {
  left: 9px;
  top: 5px;
  width: 5px;
  height: 10px;
  border: solid white;
  border-width: 0 3px 3px 0;
  -webkit-transform: rotate(45deg);
  -ms-transform: rotate(45deg);
  transform: rotate(45deg);
}
</style>
