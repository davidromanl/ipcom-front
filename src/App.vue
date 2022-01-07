<template>
  <div id="app" class="md:container md:mx-auto">
    <div v-if="Object.keys(mensaje).length==0" class="rounded bg-white shadow-lg p-4 mt-3">
      <h2 class="text-xl">Calendario</h2>
      <hr class="my-2" />
      <div class="flex justify-center my-2">
        <button
          @click="cambiaDias(false)"
          class="bg-gray-400 hover:bg-gray-700 text-white font-bold rounded"
        >
          <svg
            class="h-8 w-8"
            width="24"
            height="24"
            viewBox="0 0 24 24"
            stroke-width="2"
            stroke="currentColor"
            fill="none"
            stroke-linecap="round"
            stroke-linejoin="round"
          >
            <path stroke="none" d="M0 0h24v24H0z" />
            <polyline points="15 6 9 12 15 18" />
          </svg>
        </button>
        <span class="px-4">{{ inicio }} - {{ final }}</span>
        <button
          @click="cambiaDias(true)"
          class="bg-gray-400 hover:bg-gray-700 text-white font-bold rounded"
        >
          <svg
            class="h-8 w-8"
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            stroke-width="2"
            stroke-linecap="round"
            stroke-linejoin="round"
          >
            <polyline points="9 18 15 12 9 6" />
          </svg>
        </button>
      </div>
      <table class="table rounded border w-full">
        <thead>
          <tr>
            <th class="border p-2">Hora</th>
            <th v-for="(day, k) in days" :key="k">
              {{ day.date }} <br />
              <small>{{ dias[k] }}</small>
            </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(h, k) in horas" :key="k">
            <td class="border p-2">{{ h }}</td>
            <td
              v-for="(day, d) in days"
              :key="d"
              class="border p-1"
              :class="[day.date == formatoFecha(hoy) ? 'bg-indigo-100' : '']"
            >
              <div
                v-if="disponible(h, d)"
                class="border-2 border-red-400 rounded h-8"
                :class="[
                  day.date == fecha && h == hora
                    ? 'border-4 border-red-500 bg-red-400'
                    : 'border-2 border-red-200 bg-red-300',
                ]"
              ></div>
              <div
                v-else
                @click="
                  hora = h;
                  fecha = day.date;
                  disable = false;
                "
                class="rounded hover:bg-green-200 h-8"
                :class="[
                  day.date == fecha && h == hora
                    ? 'border-4 border-green-400 bg-green-300'
                    : 'border-2 border-green-100',
                ]"
              ></div>
            </td>
          </tr>
        </tbody>
      </table>
      <div class="flex mt-2">
        <label for="fecha" class="p-2">Seleccionado:</label>
        <input
          @change="cambiaFecha()"
          type="date"
          v-model="fecha"
          name="fecha"
          id="fecha"
          class="border rounded p-1"
        />
        <select
          class="rounded border mx-3 px-2"
          @change="validaFecha"
          :disabled="!fecha"
          v-model="hora"
        >
          <option v-for="(h, n) in horas" :key="n" :value="h">
            {{ h }}
          </option>
        </select>
        <button
          @click="agendar"
          class="bg-blue-500 hover:bg-blue-700 text-white p-2 rounded"
          :class="[
            disable || hora == '' ? 'opacity-50 cursor-not-allowed' : '',
          ]"
          :disabled="disable || hora == ''"
        >
          Agendar
        </button>
      </div>
    </div>
    <div v-else class="rounded bg-white shadow-lg p-4 mt-3">
      <div class="rounded bg-green-200 max-w-md mx-auto p-4 my-3 text-center">
        <h2 class="text-xl">Confirmación de cita</h2>
        <hr class="my-2">
        <p>Id: {{ mensaje.id }}</p>
        <p>Fecha: {{ mensaje.date }}</p>
        <p>Hora: {{ mensaje.hour }}</p>
      </div>
    </div>
  </div>
</template>

<style>
.body {
  background: blue;
}
</style>

<script>
import axios from "axios";

axios.defaults.baseURL = "https://frontend.recruit.ipcom.ai/";

axios.defaults.headers.common["Authorization"] = "Bearer njXGWXKYM2ft";

export default {
  name: "App",
  data: () => ({
    horas: [
      "08:00",
      "09:00",
      "10:00",
      "11:00",
      "12:00",
      "13:00",
      "14:00",
      "15:00",
      "16:00",
      "17:00",
      "18:00",
      "19:00",
      "20:00",
    ],
    dias: ["Lunes", "Martes", "Miercoles", "Jueves", "Viernes"],
    days: [],
    fecha: "",
    hora: "",
    inicio: "",
    final: "",
    disable: true,
    hoy: Date.now(),
    mensaje: {}
  }),

  mounted() {
    if (localStorage.id) {
      this.getSchedule(localStorage.id);
    } else {
      this.fecha = this.formatoFecha(this.hoy);
      this.getDays(this.fecha);
    }
  },

  methods: {
    async getDays(fecha) {
      var d = new Date(fecha);
      var day = d.getDay();
      d.setDate(d.getDate() - day + (day == 0 ? -6 : 1));
      this.days = [];
      const axiosDays = [];
      for (let i = 0; i < 5; i++) {
        axiosDays[i] = axios.get("freeschedule?day=" + this.formatoFecha(d));
        d.setDate(d.getDate() + 1);
      }
      const days = await axios.all(axiosDays);
      for (let i = 0; i < days.length; i++) {
        this.days.push(days[i].data);
      }
      this.inicio = this.days[0].date;
      this.final = this.days[4].date;
    },

    cambiaDias(type) {
      var d = new Date(this.inicio);
      let dir = type ? 14 : -7;
      d.setDate(d.getDate() + dir);
      this.getDays(d);
      this.hora = "";
    },

    cambiaFecha() {
      if (!this.fecha) return;
      console.log("fecha: " + this.fecha);
      const estaSemana = this.days.find((dia) => dia.date === this.fecha);
      if (estaSemana == undefined) {
        this.getDays(this.fecha);
        this.hora = "";
      } else {
        this.validaFecha();
      }
    },

    formatoFecha(val) {
      const _time = new Date(val);
      const _ano = _time.getFullYear();
      const _mes =
        _time.getMonth() < 9
          ? "0" + (_time.getMonth() + 1)
          : _time.getMonth() + 1;
      const _dia =
        _time.getDate() < 10 ? "0" + _time.getDate() : _time.getDate();
      return `${_ano}-${_mes}-${_dia}`;
    },

    disponible(hora, dia) {
      return this.days[dia].scheduled.includes(hora);
    },

    validaFecha() {
      const estaSemana = this.days.find((dia) => dia.date === this.fecha);
      if (estaSemana) this.disable = estaSemana.scheduled.includes(this.hora);
    },

    async agendar() {
      const item = { date: this.fecha, hour: this.hora };
      const { data } = await axios.post("schedule", item);
      localStorage.id = data.id;
      this.mensaje = data
    },

    async getSchedule(id) {
      const { data } = await axios.get("schedule/" + id);
      this.mensaje = data
    },
  },
};
</script>

<style>
</style>
