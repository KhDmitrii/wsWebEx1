<template>
  <div class="container">
    <div class="row">
      <div class="col-12">
        <h1>Супер форма добавления чего-нить</h1>
      </div>
      <div class="col-12">
        <div class="form-group">
          <label for="d_type">Выберите тип устройства</label>
          <select v-model="dType" class="form-control" id="d_type">
            <option value="1">Видеокарта</option>
            <option value="2">Не видеокарта</option>
            <option value="3">Совсем не видеокарта</option>
          </select>
        </div>
      </div>
      <div class="col-12">
        <div class="form-group">
          <label for="d_name">Название*</label>
          <input
            v-model="dName"
            type="text"
            class="form-control"
            id="d_name"
            name="d_name"
            placeholder="Название"
            required
          />
        </div>
      </div>
      <div class="col-12">
        <div class="form-group">
          <label for="d_pict">Картинка</label>
          <input
            v-on:change="handleFileUpload()"
            ref="file"
            type="file"
            class="form-control-file"
            id="d_pict"
            accept="image/*"
          />
          <!-- не забудь валидацию по типу (accept) -->
        </div>
      </div>
      <div class="col-12" v-if="dFile !== null">
        <p>Так base64 можно отобразить</p>
        <img v-bind:src="dFile" alt="картинка" />
      </div>
      <div class="col-12 mb-2">
        <button
          class="btn btn-primary"
          v-on:click="onSend"
          v-bind:disabled="dName === ''"
        >
          Отправить
        </button>
      </div>
      <div class="col-12">
        <button class="btn btn-primary" v-on:click="onLogJson">
          Что в джейсоне?
        </button>
      </div>
    </div>
  </div>
</template>

<script>
const toBase64 = (file) =>
  new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.readAsDataURL(file);
    reader.onload = () => resolve(reader.result);
    reader.onerror = (error) => reject(error);
  });
import json from './json/test.json';
// так подключаем json внешние
export default {
  name: 'App',
  data() {
    return {
      myJson: json, // объявляем импортированный json
      dName: '',
      dType: '1',
      dFile: null,
    };
  },
  methods: {
    async handleFileUpload() {
      let file = this.$refs.file.files[0];
      this.dFile = await toBase64(file).catch((e) => Error(e));
    },
    onSend() {
      let obj = {
        name: this.dName,
        type: this.dType,
        file: this.dFile,
      };
      console.log(obj);
    },
    onLogJson() {
      console.log(this.myJson);
      console.log(this.myJson.name);
      // работаем с json как с объектом
    },
  },
};
</script>

<style></style>
