<script>
import { ref } from "vue";
import { langCodeWithName } from "./data";
import axios from "axios";

export default {
  setup() {
    const source = ref();
    const targetTranslate = ref();
    const result = ref();
    const loading = ref(false);

    const detect_url =
      "https://google-translate1.p.rapidapi.com/language/translate/v2/detect";
    const translate_url =
      "https://google-translate1.p.rapidapi.com/language/translate/v2";

    const headers = {
      "content-type": "application/x-www-form-urlencoded",
      "X-RapidAPI-Key": "170f4de719mshe105e903c055698p19952bjsn1bb5db2de152",
      "X-RapidAPI-Host": "google-translate1.p.rapidapi.com",
    };

    const translate = async () => {
      loading.value = true;

      const encodedParams = new URLSearchParams();
      encodedParams.append("q", source.value);
      // console.log(source.value);
      // console.log(targetTranslate.value);

      const options = {
        method: "POST",
        url: detect_url,
        headers,
        data: encodedParams,
      };

      const response = await axios.request(options);
      // console.log(response.data);
      const source_key = response.data.data.detections[0][0].language;

      const encodedParamsTranslate = new URLSearchParams();
      encodedParamsTranslate.append("q", source.value);
      encodedParamsTranslate.append("target", targetTranslate.value);
      encodedParamsTranslate.append("source", source_key);

      if (!source.value || !targetTranslate.value || !source_key) {
        alert("Please select option");
        return (loading.value = false);
      }

      const optionsTranslate = {
        method: "POST",
        url: translate_url,
        headers,
        data: encodedParamsTranslate,
      };

      const responseTranslate = await axios.request(optionsTranslate);
      // console.log(responseTranslate.data);
      result.value = responseTranslate.data.data.translations[0].translatedText;
      loading.value = false;
    };

    return {
      langCodeWithName,
      source,
      targetTranslate,
      result,
      translate,
      loading,
    };
  },
};
</script>

<template>
  <div class="hero">
    <textarea
      name=""
      id=""
      cols="10"
      rows="5"
      placeholder="Write Something..."
      v-model="source"
    ></textarea>
    <select name="" id="" v-model="targetTranslate">
      <option v-for="item in langCodeWithName" :value="item.code">
        {{ item.name }}
      </option>
    </select>
    <textarea
      name=""
      id=""
      cols="10"
      rows="5"
      placeholder="Result"
      v-model="result"
    ></textarea>
    <button @click="translate" v-if="!loading">Translate</button>
    <button v-else>Loading ...</button>
  </div>
</template>

<style></style>
