<template>
  <div class="hitokoto cards" @mouseenter="openMusicShow = true"
    @mouseleave="openMusicShow = false" @click.stop>
    <!-- 打开音乐面板 -->
    <Transition name="el-fade-in-linear">
      <div class="open-music" v-show="openMusicShow && store.musicIsOk" @click="store.musicOpenState = true">
        <music-menu theme="filled" size="18" fill="#efefef" />
        <span>打开音乐播放器</span>
      </div>
    </Transition>
    <!-- 一言内容 -->
    <Transition name="el-fade-in-linear" mode="out-in">
      <div :key="myInfoData.text" class="content" @click="getNewInfoData">
        <span class="text">{{ myInfoData.text }}</span>
        <span :v-if="myInfoData.text2 != ''" class="text">&nbsp;{{ myInfoData.text2 }}&nbsp;</span>
      </div>
    </Transition>
  </div>
</template>

<script setup>
import { MusicMenu, Error } from "@icon-park/vue-next";
import { getHitokoto } from "@/api";
import { mainStore } from "@/store";
import debounce from "@/utils/debounce.js";

const store = mainStore();

// 开启音乐面板按钮显隐
const openMusicShow = ref(false);

// 一言数据
const hitokotoData = reactive({
  text: "这里应该显示一句话",
  from: "無名",
});

const myInfoData = reactive({
  text: "这里应该显示一句话",
  text2: "無名",
});

const myInfoArray = [
  {
    text: '与光同尘',
    text2: 'With Light'
  },
  {
    text: '永远年轻，永远声名狼藉',
    text2: ''
  }
]

var randomIndex = -1;

function getNewInfoData() {
  if (randomIndex == -1) {
    randomIndex = Math.floor(Math.random() * myInfoArray.length); // 生成随机索引
  }
  else {
    if (randomIndex < myInfoArray.length - 1) {
      ++randomIndex;
    } else {
      randomIndex = 0;
    }
  }
  console.log(randomIndex);
  myInfoData.text = myInfoArray[randomIndex].text; // 根据随机索引获取随机值并赋给randomValue变量
  myInfoData.text2 = myInfoArray[randomIndex].text2; // 根据随机索引获取随机值并赋给randomValue变量
};

// 获取一言数据
const getHitokotoData = () => {
  getHitokoto()
    .then((res) => {
      hitokotoData.text = res.hitokoto;
      hitokotoData.from = res.from;
    })
    .catch(() => {
      ElMessage({
        message: "一言获取失败",
        icon: h(Error, {
          theme: "filled",
          fill: "#efefef",
        }),
      });
      hitokotoData.text = "这里应该显示一句话";
      hitokotoData.from = "無名";
    });
};

// 更新一言数据
const updateHitokoto = () => {
  // 防抖
  debounce(() => {
    getHitokotoData();
  }, 500);
};

onMounted(() => {
  getNewInfoData();
  getHitokotoData();
});
</script>

<style lang="scss" scoped>
.hitokoto {
  width: 100%;
  height: 100%;
  padding: 20px;
  animation: fade 0.5s;

  .open-music {
    width: 100%;
    position: absolute;
    top: 0;
    left: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    background: #00000026;
    padding: 4px 0;
    border-radius: 8px 8px 0 0;

    .i-icon {
      width: 18px;
      height: 18px;
      display: block;
      margin-right: 8px;
    }

    span {
      font-size: 0.95rem;
    }
  }

  .content {
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: space-evenly;

    .text {
      font-size: 1.1rem;
      word-break: break-all;
      text-overflow: ellipsis;
      overflow: hidden;
      display: flex;
      -webkit-line-clamp: 3;
      -webkit-box-orient: vertical;
      justify-content: center;
    }

    .from {
      margin-top: 10px;
      font-weight: bold;
      align-self: flex-end;
      font-size: 1.1rem;
    }
  }
}
</style>
