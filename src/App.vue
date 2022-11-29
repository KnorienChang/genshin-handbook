<script setup lang="ts">
import { onMounted, ref } from "vue";

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
const checkedCharater = ref<string>("Nahida");
const screenWidth = ref(document.body.clientWidth);

const changeHandler = (name: string) => {
  checkedCharater.value = name;
};

window.onresize = () => {
  screenWidth.value = document.body.clientWidth;
};

onMounted(async () => {
  try {
    characters.value = await (await fetch("/characters.json")).json();
  } catch (error) {
    console.log(error);
  }
});
</script>

<template>
  <div style="padding: 8px 16px">
    <n-descriptions label-placement="left" title="写在前面">
      <n-descriptions-item label="头像数据">
        <a href="https://genshin-impact.fandom.com/wiki/Genshin_Impact_Wiki" target="_blank">Genshin Impact Wiki</a>
      </n-descriptions-item>
      <n-descriptions-item label="攻略数据">
        <a href="https://bbs.mihoyo.com/ys/accountCenter/postList?id=74019947" target="_blank">猫冬</a>
      </n-descriptions-item>
    </n-descriptions>
  </div>
  <div class="charater">
    <ul>
      <li v-for="{ name, cname, avatar } in characters.filter(
        ({ rarity }) => rarity === 5
      )" :key="name" :class="checkedCharater === name ? 'checked' : ''" @click="() => changeHandler(name)" :style="{
  backgroundImage:
    'url(https://static.wikia.nocookie.net/gensin-impact/images/e/ea/Rarity_5_background.png/revision/latest)',
  color: RARITY_COLORS.r5,
}">
        <figure>
          <img :src="avatar" :alt="cname" />
          <figcaption>
            {{ cname }}
          </figcaption>
        </figure>
      </li>
    </ul>
    <ul>
      <li v-for="{ name, cname, avatar } in characters.filter(
        ({ rarity }) => rarity === 4
      )" :key="name" :class="checkedCharater === name ? 'checked' : ''" @click="() => changeHandler(name)" :style="{
  backgroundImage:
    'url(https://static.wikia.nocookie.net/gensin-impact/images/c/c9/Rarity_4_background.png/revision/latest)',
  color: RARITY_COLORS.r4,
}">
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
    <template v-for="url in characters.find(({ name }) => name === checkedCharater)
    ?.handbook ?? []">
      <n-image :width="screenWidth" :src="url" />
    </template>
  </div>
</template>

<style lang="less" scoped>
ul,
li {
  margin: 0;
  padding: 0;
  list-style: none;
}

.charater {
  padding: 8px 16px;

  ul {
    display: flex;
    flex: 1;
    flex-wrap: wrap;
    gap: 8px;

    &+ul {
      margin-top: 8px;
    }

    li {
      border: 1px solid transparent;
      border-radius: 4px;
      overflow: hidden;
      box-shadow: 0 0 1px 2px #d8edff, 0 0 1px #d8edff inset;
      cursor: pointer;
      transition: all 0.15s;
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
