<script setup lang="ts">
import { onMounted, ref, watch } from "vue";

interface Characters {
  name: string;
  cname: string;
  rarity: number;
  avatar: string;
  imgSrc?: string;
  handbook?: string | string[];
}

enum RARITY_COLORS {
  "r5" = "#9a6d43",
  "r4" = "#917ab1",
}

const characters = ref<Characters[]>([]);
const checkedCharater = ref<string>();
const screenWidth = ref(document.body.clientWidth);
const imageLoaded = ref<boolean>(false);

const changeHandler = (name: string) => {
  checkedCharater.value = name;
  imageLoaded.value = true;
};

const imageLoadedHandler = (e: Event) => {
  imageLoaded.value = false;
};

window.onresize = () => {
  screenWidth.value = document.body.clientWidth;
};

onMounted(async () => {
  try {
    characters.value = await (
      await fetch(`${import.meta.env.VITE_BASE_PATH}/characters.json`)
    ).json();
  } catch (error) {
    console.log(error);
  }
});

watch(
  () => characters.value,
  (value) => {
    if (value?.length > 0) {
      checkedCharater.value = value?.[0].name;
      imageLoaded.value = true;
    }
  }
);
</script>

<template>
  <div style="padding: 8px 16px">
    <n-descriptions label-placement="left" title="写在前面">
      <n-descriptions-item label="头像数据">
        <a href="https://bbs.mihoyo.com/ys" target="_blank">米游社·原神</a>
      </n-descriptions-item>
      <n-descriptions-item label="攻略数据">
        <a
          href="https://bbs.mihoyo.com/ys/accountCenter/postList?id=74019947"
          target="_blank"
          >猫冬</a
        >
      </n-descriptions-item>
    </n-descriptions>
  </div>
  <div class="charater">
    <ul>
      <li
        v-for="{ name, cname, avatar } in characters.filter(
          ({ rarity }) => rarity === 5
        )"
        :key="name"
        :class="checkedCharater === name ? 'checked' : ''"
        @click="() => changeHandler(name)"
        :style="{
          color: RARITY_COLORS.r5,
        }"
      >
        <figure>
          <img :src="avatar" :alt="cname" />
          <figcaption>
            {{ cname }}
          </figcaption>
        </figure>
      </li>
    </ul>
    <ul>
      <li
        v-for="{ name, cname, avatar } in characters.filter(
          ({ rarity }) => rarity === 4
        )"
        :key="name"
        :class="checkedCharater === name ? 'checked' : ''"
        @click="() => changeHandler(name)"
        :style="{
          color: RARITY_COLORS.r4,
        }"
      >
        <figure>
          <img :src="avatar" :alt="cname" />
          <figcaption>
            {{ cname }}
          </figcaption>
        </figure>
      </li>
    </ul>
  </div>

  <div class="handbook">
    <template
      v-for="url in characters.find(({ name }) => name === checkedCharater)
        ?.handbook ?? []"
    >
      <n-spin :show="imageLoaded">
        <n-image
          :width="screenWidth"
          :src="url"
          lazy
          @load="imageLoadedHandler"
        />
      </n-spin>
    </template>
  </div>
</template>

<style lang="less" scoped>
ul,
li {
  margin: 0;
  padding: 3px;
  list-style: none;
}

.charater {
  padding: 8px 16px;

  ul {
    display: flex;
    flex: 1;
    flex-wrap: wrap;
    gap: 8px;

    & + ul {
      margin-top: 8px;
    }

    li {
      border: 1px solid transparent;
      border-radius: 4px;
      overflow: hidden;
      box-shadow: 0 0 1px 2px #d8edff, 0 0 1px #d8edff inset;
      cursor: pointer;
      transition: all 0.15s;
      background: url(data:image/png;base64,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)
        no-repeat center/100% 100%;
      background-size: cover;

      &.checked {
        box-shadow: 0 0 1px 3px #00c8ff, 0 0 1px #00c8ff inset;
      }
    }

    figure {
      margin: 0;
      text-align: center;

      img {
        width: 80px;
      }
    }
  }
}

.handbook {
  margin-top: 16px;
  text-align: center;
}
</style>
