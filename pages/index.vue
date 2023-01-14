<template>
  <div class="text-center">
    <v-row
      class="mt-10"
      style="height: 30vh;"
      align-content="center" justify="center"
    >
      <v-col
        sm="12"
        md="6"
        lg="6"
      >
        <v-text-field
          v-model="url"
          label="変換前URL"
          placeholder="https://example.com?a=1"
          outlined
          prepend-inner-icon="mdi-link"
        />
      </v-col>
    </v-row>
    <v-row
      style="height: 10vh;"
      align-content="center" justify="center"
    >
      <v-col
        sm="12"
        md="6"
        lg="6"
      >
        <v-text-field
          label="変換後URL（読み取り専用）"
          placeholder="https://example.com"
          outlined
          readonly
          :value="getFormattedUrl"
          prepend-inner-icon="mdi-link"
        />
      </v-col>
    </v-row>
    <v-row
      style="height: 10vh;"
      align-content="center" justify="center"
    >
      <v-btn
        color="primary"
        :disabled="!isUrl()"
        @click="copy"
      >
        COPY
      </v-btn>
    </v-row>

    <v-snackbar
      v-model="snackbar"
      :timeout="3000"
      :color="color"
      outlined
    >
      {{ message }}
      <template #action="{ attrs }">
        <v-btn
          text
          v-bind="attrs"
          @click="snackbar = false"
        >
          Close
        </v-btn>
      </template>
    </v-snackbar>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
  data() {
    return {
      url: '',
      urlRegExp: /https?:\/\/[-_.!~*'()a-zA-Z0-9;/?:@&=+$,%#]+/g,
      snackbar: false,
      message: '',
      color: '',
    }
  },
  computed: {
    getFormattedUrl() {
      if (!this.isUrl()) return '';
      const resultUrl = new URL(this.url);
      const { searchParams } = resultUrl;
      // クエリパラメータを削除
      searchParams.forEach((_, k) => {
        resultUrl.searchParams.delete(k);
      })
      const urlStr = resultUrl.origin + resultUrl.pathname + (resultUrl.pathname.at(-1) === '/' ? '' : '/');
      return urlStr + 'video/1';
    }
  },
  methods: {
    // クリップボードにコピー
    copy() {
      navigator.clipboard.writeText(this.getFormattedUrl).then(
      () => {
        this.message = 'コピーしました。';
        this.color = 'success';
      },
      () => {
        this.message = 'コピーできませんでした';
        this.error = 'error';
      });
      this.snackbar = true
    },
    isUrl() {
      return this.url.match(this.urlRegExp)
    },
  },
}
</script>
