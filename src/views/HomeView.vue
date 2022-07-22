<template>
  <div
    id="container"
    class="w-screen h-screen flex items-center justify-center mw-regular relative"
  >
    <div
      id="uploader"
      class="w-1/4 h-2/3 bg-white rounded-3xl opacity-90 px-8 py-8 flex flex-col"
      :class="{ 'move-to-left': !isInit }"
    >
      <div class="basis-2/5">
        <label
          for="message"
          class="block mb-2 text-xl font-medium text-gray-900 dark:text-gray-400"
          >Text</label
        >
        <div class="h-1"></div>
        <textarea
          id="message"
          rows="6"
          class="block p-2.5 w-full text-sm text-gray-900 bg-gray-50 rounded-lg border border-gray-300 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
          placeholder="Your text..."
          v-model="text"
        ></textarea>
      </div>

      <div class="basis-3/5">
        <div
          class="block mb-2 text-xl font-medium text-gray-900 dark:text-gray-300"
        >
          Picture
        </div>
        <div class="h-1"></div>
        <div class="flex justify-center items-center w-full">
          <label
            for="file"
            class="flex flex-col justify-center items-center w-full h-56 bg-gray-50 rounded-lg border-2 border-gray-300 border-dashed cursor-pointer dark:hover:bg-bray-800 dark:bg-gray-700 hover:bg-gray-100 dark:border-gray-600 dark:hover:border-gray-500 dark:hover:bg-gray-600"
          >
            <div class="flex flex-col justify-center items-center pt-5 pb-6">
              <svg
                class="mb-3 w-10 h-10 text-gray-400"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24"
                xmlns="http://www.w3.org/2000/svg"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12"
                ></path>
              </svg>
              <p class="mb-2 text-sm text-gray-500 dark:text-gray-400">
                <span class="font-semibold">Click to upload</span> or drag and
                drop
              </p>
              <p class="text-xs text-gray-500 dark:text-gray-400">
                PNG, JPG or GIF
              </p>
            </div>
            <input
              id="file"
              type="file"
              class="hidden"
              ref="uploadFile"
              @change="getFile"
            />
            <div class="text-gray-500 text-sm">
              {{ showFilepath ? `file path: ${filepath}` : "No picture now." }}
            </div>
          </label>
        </div>
      </div>
      <div class="flex justify-center">
        <button
          type="button"
          class="w-2/5 py-2.5 px-5 mr-2 mb-2 text-sm font-medium text-gray-900 focus:outline-none bg-white rounded-full border border-gray-200 hover:bg-gray-100 hover:text-blue-700"
          @click="move"
        >
          Upload
        </button>
      </div>
    </div>
    <div
      id="information"
      class="w-1/4 h-2/3 bg-white rounded-3xl opacity-90 px-8 py-8 flex flex-col items-center justify-center absolute right-80 opacity-0"
      :class="{ appear: !isInit }"
    >
      <div class="text-xl">Music: {{ song }}</div>
      <br />
      <div class="text-xl">Singer: {{ singer }}</div>
    </div>
  </div>
</template>

<script setup lang="ts">
import axios from "axios";
import { requestUrl } from "@/http";
import { ref } from "vue";

const isInit = ref(true);

const text = ref("");
const uploadFile = ref("uploadFile");

const showFilepath = ref(false);
const filepath = ref("");

const song = ref("");
const singer = ref("");

const getFile = () => {
  // eslint-disable-next-line @typescript-eslint/ban-ts-comment
  // @ts-ignore: Uncheck
  // filepath.value = uploadFile.value.value;
  filepath.value = uploadFile.value.files[0].name;
  showFilepath.value = true;
  console.log(`Get picture ${filepath.value}`);
};

const move = () => {
  // eslint-disable-next-line @typescript-eslint/ban-ts-comment
  // @ts-ignore: Uncheck
  const files = uploadFile.value.files;
  if (text.value === "" && files.length !== 1) {
    console.log("Empty text or picture");
    return;
  }
  // TODO upload
  // song.value = "Teeth";
  // singer.value = "5 Seconds Of Summer";
  // TODO upload
  console.log(files[0]);
  const formData = new FormData();
  formData.append("file", files[0]);
  axios
    .post(`${requestUrl}/upload/text/${text.value}`, formData, {
      headers: {
        "Content-Type": "multipart/form-data",
      },
    })
    .then((res) => {
      console.log(res);
      song.value = res.data.song;
      singer.value = res.data.singer;
    });
  isInit.value = false;
};
</script>

<style scoped>
@import url(/src/css/font.css);

#container {
  background-image: url("/public/img/post.jpeg");
}

.move-to-left {
  animation: uploader-to-left 1.5s;
  transform: translateX(-70%);
}

@keyframes uploader-to-left {
  from {
    transform: translateX(0%);
  }

  to {
    transform: translateX(-70%);
  }
}

.appear {
  animation-name: information-appear;
  animation-duration: 1.5s;
  opacity: 90%;
}

@keyframes information-appear {
  from {
    opacity: 0;
  }

  to {
    opacity: 90%;
  }
}
</style>
