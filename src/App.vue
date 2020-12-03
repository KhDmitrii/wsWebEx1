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
      <small>все кнопки в консоль данные выводят</small>
    </div>
    <div class="button btn btn-primary" v-on:click="preparePicts">
      Получить картинки
    </div>
    <div class="wrap" v-on:mouseup="onStopDragging" v-on:mousemove="onDragging">
      <div
        class="item"
        v-for="(item, index) in items"
        :key="item.name"
        v-bind:style="{ left: item.x + 'px', top: item.y + 'px' }"
        v-on:mousedown="onStartDrag(item)"
        v-on:mousemove="onstopPropagation"
        v-on:mouseup="onStopDragging"
      >
        {{ index }}
      </div>
      <div
        class="cart"
        v-on:mousemove="onGetToCart"
        v-on:mouseup="onStopDragging"
      ></div>
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
      items: [
        { name: 'item1', x: 0, y: 0 },
        { name: 'item2', x: 0, y: 0 },
        { name: 'item3', x: 0, y: 0 },
      ],
    };
  },
  methods: {
    onGetToCart(event) {
      event.stopPropagation();
      this.items.forEach((item, index) => {
        if (item.isDragging) {
          if (item.fits) {
            item.isDragging = false;
          } else {
            item.isDragging = false;
            item.x = index * 40;
            item.y = 0;
          }
        }
      });
    },
    onstopPropagation(event) {
      event.stopPropagation(); // исправляем тот костыль
    },
    onDragging(event) {
      this.items.forEach((item) => {
        if (item.isDragging) {
          item.x = event.offsetX;
          item.y = event.offsetY;
        }
      });
    },
    onStartDrag(item) {
      item.isDragging = true;
    },
    onStopDragging() {
      this.items.forEach((item, index) => {
        if (item.isDragging) {
          item.x = index * 40;
          item.y = 0;
          item.isDragging = false;
        }
      });
    },
    preparePicts() {
      this.items.forEach((item, index) => {
        item.x = index * 40;
        item.y = 0;
        item.fits = index % 2 ? true : false; // это замени алгоритмом для вычисления пригодности
        item.isDragging = false;
      });
    },
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

<style>
.wrap {
  position: relative;
  width: 100%;
  height: 400px;
}
.cart {
  position: absolute;
  bottom: 0px;
  height: 50px;
  width: 100%;
  background: red;
  z-index: 1;
}
.item {
  position: absolute;
  top: 0px;
  left: 10px;
  width: 30px;
  height: 30px;
  background: green;
  border: 1px solid black;
  cursor: pointer;
  user-select: none;
  z-index: 2;
}
</style>
