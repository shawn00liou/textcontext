<template>
  <q-page class="row items-center justify-evenly" @click="afterTextChange">
    <q-card class="col-6 flex flex-center">
      <q-card-section class="row">
        <q-input filled v-model="findText" label="Find" class="col"></q-input>
        <q-input filled v-model="replaceText" label="Replace" class="col"></q-input>
      </q-card-section>
      <q-card-section class="q-pa-none">
        <textarea class="has-error content" v-model.lazy="inputText" @change.prevent.stop="afterTextChange"></textarea>
      </q-card-section>
      <q-card-section class="q-pa-none full-width row">
        <q-btn flat label="Touch" class="col"></q-btn>
        <q-btn flat label="Copy" class="col" @click.stop="onCopy"></q-btn>
        <q-btn flat label="Clean" class="col" @click.stop="onClean"></q-btn>
      </q-card-section>
    </q-card>
  </q-page>
</template>

<script lang="ts">
import { Vue, Component } from 'vue-property-decorator';
import siteconfigDemoJson from 'app/siteconfig.demo.json';
import DefaultDialog from '../components/_global/dialog/DefaultDialog.vue';
import { copyToClipboard } from 'quasar';
// 定義 CordovaPlugins 需要的interface
interface ClipboardFunc {
  (t: string, succ: VoidFunction, err: VoidFunction | null): void;
}

interface Clipboard {
  copy: ClipboardFunc;
}

interface CordovaPlugins {
  clipboard?: Clipboard;
}

// 正文開始
@Component<Index>({
  name: 'Index'
})
export default class Index extends Vue {
  public inputText = '';
  public findText = '';
  public replaceText = '';

  get baseJson() {
    return siteconfigDemoJson;
  }

  public afterTextChange() {
    const rand = Math.floor(Math.random() * 1000);
    const choose = rand % 2;
    const symbol = [this.baseJson.pages.symbol, this.baseJson.pages.replace];

    const continuousR = Math.floor(Math.random() * 1000) % symbol[choose].continuous.length;
    const periodR = Math.floor(Math.random() * 1000) % symbol[choose].period.length;
    const endparticleR = Math.floor(Math.random() * 1000) % symbol[choose].endparticle.length;

    const inputList = [...this.inputText, '']; // 結尾給他一個尾字

    // 首字不判斷
    inputList.reduce((accumulator, currentValue, currentIndex, arrlist) => {
      const currentPeriod = currentIndex < arrlist.length - 1 ? periodR : 0;
      // 符號
      const findIndex = symbol[choose === 1 ? 0 : 1].continuous.findIndex((element) => element === currentValue);
      currentValue = findIndex >= 0 ? symbol[choose].continuous[continuousR] : currentValue;

      const findPeriod = symbol[choose === 1 ? 0 : 1].period.findIndex((element) => element === currentValue);
      currentValue = findPeriod >= 0 ? symbol[choose].period[currentPeriod] : currentValue;

      const findEndparticle = symbol[choose === 1 ? 0 : 1].endparticle.findIndex((element) => element === currentValue);
      currentValue = findEndparticle >= 0 ? symbol[choose].endparticle[endparticleR] : currentValue;

      // word
      const findWordEndparticle = this.baseJson.world.endparticle.findIndex((element) => element === accumulator); // 因為是結尾

      // 空格
      const reg = /\s/;
      if (reg.test(currentValue)) {
        // 如果是最末則句點,其他則可能為句號 或是分號做連詞
        arrlist[currentIndex - 1] = `${accumulator}${symbol[choose].period[currentPeriod]}`;
        currentValue = '';
      } else {
        // 因為是結尾 所以不走空格
        currentValue =
          findWordEndparticle >= 0 && currentValue !== symbol[choose].endparticle[0]
            ? `${currentValue}${symbol[choose].endparticle[0]}`
            : currentValue;
      }

      arrlist[currentIndex] = currentValue;
      return currentValue;
    });

    const context = inputList.join('');

    this.inputText = this.findText && this.replaceText ? context.split(this.findText).join(this.replaceText) : context;
  }

  public onClean() {
    this.inputText = '';
  }

  public onCopy() {
    try {
      const plugins: CordovaPlugins = cordova && cordova.plugins;
      const clipboard = plugins.clipboard;
      clipboard &&
        clipboard.copy(
          this.inputText,
          () => {
            this.$q.dialog({
              parent: this,
              component: DefaultDialog,
              content: 'copy success!!!'
            });
          },
          null
        );
    } catch (er) {
      this.webOnCopy(); // for web browser
    }
  }

  public webOnCopy() {
    copyToClipboard(this.inputText)
      .then(() => {
        this.$q.dialog({
          parent: this,
          component: DefaultDialog,
          content: 'copy success!!!'
        });
      })
      .catch(() => {
        //
      });
  }
}
</script>

<style lang="scss" scoped>
.content {
  height: 50vh;
  width: 50vw;
}
</style>
