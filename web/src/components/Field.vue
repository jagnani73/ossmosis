<template>
  <div class="w-full">
    <div class="mb-4">
      <label
        class="block text-gray-500 font-bold mb-1 md:mb-0"
        for="inline-full-name"
      >
        {{ field.name }}
      </label>

      <div
        class="flex items-center bg-gray-200 border-2 rounded w-full px-2"
        :class="error.length > 0 ? 'border-red-400' : 'border-gray-200'"
      >
        <span class="text-blueGray-300 w-5" v-if="icon">
          <UserIcon v-if="icon === 'UserIcon'" class="h-5 w-5 text-black" />
          <HashtagIcon
            v-else-if="icon === 'HashtagIcon'"
            class="h-full w-full text-black"
          />
          <MailIcon
            v-else-if="icon === 'MailIcon'"
            class="h-full w-full text-black"
          />
          <GithubSVG
            v-else-if="icon === 'GithubSVG'"
            class="h-full w-full text-black"
          />
          <LinkIcon
            v-else-if="icon === 'LinkIcon'"
            class="h-full w-full text-b lack"
          />
          <AnnotationIcon
            v-else-if="icon === 'AnnotationIcon'"
            class="h-full w-full text-black"
          />
          <LockClosedIcon
            v-else-if="icon === 'LockClosedIcon'"
            class="h-full w-full text-black"
          />
        </span>

        <textarea
          v-if="field.textarea"
          class="
            w-full
            bg-gray-200
            border-2
            rounded
            py-2
            text-gray-700
            focus:outline-none
            resize-none
            h-24
          "
          v-model="fielddata"
          type="text"
          :placeholder="field.placeholder"
          @change="mutate"
          @blur="handleBlur"
        />

        <input
          v-else
          class="w-full p-2 bg-transparent text-gray-700 focus:outline-none"
          v-model="fielddata"
          type="text"
          :placeholder="field.placeholder"
          @change="mutate"
          @blur="handleBlur"
        />
      </div>

      <span v-if="error.length > 0" class="text-xs text-red-500 font-bold">
        {{ error }}
      </span>
    </div>
  </div>
</template>

<script>
import { validate } from "./validate";
import {
  UserIcon,
  MailIcon,
  AnnotationIcon,
  LockClosedIcon,
} from "@heroicons/vue/solid";
import { HashtagIcon, LinkIcon } from "@heroicons/vue/outline";
import GithubSVG from "./SVG/githubsvg";

export default {
  name: "Field",
  props: ["field", "icon"],
  components: {
    UserIcon,
    MailIcon,
    HashtagIcon,
    GithubSVG,
    AnnotationIcon,
    LinkIcon,
    LockClosedIcon,
  },
  data() {
    return {
      fielddata: "",
      error: "",
    };
  },
  mounted() {
    sessionStorage.getItem("formdata")
      ? (this.fielddata = JSON.parse(sessionStorage.getItem("formdata"))[
          this.$props.field["index"]
        ])
      : (this.fielddata = "");
  },
  methods: {
    updateField(value) {
      this.fielddata = value;
    },
    mutate: async function () {
      this.error = await validate(
        this.$props.field.name,
        this.fielddata ? this.fielddata.trim() : ""
      );
      this.$emit("mutate", { error: this.error, data: this.fielddata.trim() });
    },
    async handleBlur() {
      this.error = await validate(
        this.$props.field.name,
        this.fielddata ? this.fielddata.trim() : ""
      );
    },
  },
};
</script>
