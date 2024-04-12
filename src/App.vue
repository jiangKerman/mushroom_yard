<template>
  <div class="主容器">
    <!--  <el-text>欢迎来到平菇院，种植你的专属平菇</el-text>-->
    <!--  标题和平菇图片-->
    <div>
      <div class="主页标题容器">
        <el-text class="字体颜色" size="large">院子里共有 {{ 状态.现有总数.toFixed(1) }} 个平菇</el-text>
        <el-text class="字体颜色">{{ 状态.总效率.toFixed(1) }} 个/秒</el-text>
      </div>
      <div class="主页内容容器" style="position: relative;text-align: center">
        <el-image
            style="width:100%;height:100%;position: absolute;left: 0;top: 0;filter: brightness(100%) contrast(100%) grayscale(30%);"
            src="https://2b.zol-img.com.cn/product/139/269/cedd3aQPoljU.jpg"></el-image>
        <el-image style="height: 30vh;" src="/平菇.webp"></el-image>
        <Plus style="width: 3em;background-color: rgba(0,0,0,0.5);
      color: white;position: absolute;left: 50%;top: 50%;
      transform: translate(-50%,-50%);border-radius: 50%"
              @click="状态.现有总数=状态.现有总数+状态.自己种植.效率"></Plus>
      </div>
    </div>

    <!--    收益器材-->
    <div style="display: flex;flex-direction: column;flex-grow: 1;overflow-y: auto">
      <template v-for="(val,key) in 状态.被动收益" :key="key">
        <div v-if="val.是否展示" :style="`background-image: url(${val.背景}); background-size: cover;`">
          <template v-if="val.是否展示">
            <div class="被动收益标题">
              <el-text class="字体颜色">{{ key }} : {{ val.单个效率 }}平菇/个/秒</el-text>
              <el-text class="字体颜色">共{{ val.总效率.toFixed(1) }}/秒</el-text>
            </div>
            <div class="被动收益内容">
              <div style="display: flex;flex-grow: 1;flex-shrink: 1;">
                <el-image style="max-height: 100%;" fit="scale-down" :src="val.立绘"
                          v-for="i in Math.min(val.现有数,15)"></el-image>
              </div>
              <div v-if="val.现有数>15" style="display: flex;align-items: center">
                <!--                <el-text>-->
                <el-icon class="字体颜色">
                  <CloseBold/>
                </el-icon>
                <!--                </el-text>-->
                <el-text class="字体颜色">{{ val.现有数 }}</el-text>
              </div>
              <div
                  style="display: flex;flex-direction: column;justify-content: space-around;margin-right: 1em">
                <el-icon class="被动收益图标" @click="购买(key,val)">
                  <Plus></Plus>
                </el-icon>
                <el-tooltip
                    :content="`花费${val.单价}: ${val.描述}`"
                    placement="left"
                >
                  <el-icon class="被动收益图标">
                    <QuestionFilled/>
                  </el-icon>
                </el-tooltip>
              </div>
            </div>
          </template>
        </div>
      </template>
    </div>

    <!--    底部按钮-->
    <div style="display: flex;justify-content: space-around;">
      <!--      提升种植效率-->
      <div v-if="状态.被动收益.园丁.现有数>0" style="display: flex;flex-direction: row;align-items: center">
        <el-button @click="提高种植效率" :disabled="状态.现有总数<状态.自己种植.单价">提升效率</el-button>
        <el-icon>
          <InfoFilled/>
        </el-icon>
      </div>

      <!--      统一全球平菇市场-->
      <div>
        <template v-if="状态.最高总数>500">
          <el-tooltip
              :content="`花费500个平菇统一全球平菇市场,让我院生产效率永久翻倍!`"
              placement="top"
          >
            <el-button @click="统一全球评估市场"
                       :disabled="状态.是否统一全球评估市场 || 状态.现有总数<500">{{
                状态.是否统一全球评估市场 ? '你已经统一全球平菇市场' : '统一全球平菇市场!'
              }}
            </el-button>
          </el-tooltip>
        </template>
      </div>

      <!--      通关！-->
      <div>
        <el-button
            @click="ElMessage.success('恭喜!您平菇院的大名在全宇宙无人不知,无人不晓。现在，全宇宙的平菇都是您的了！')">
          让你的平菇大院在宇宙各个角落生根发芽!生生不息!
        </el-button>
      </div>

      <!--      <template v-if="状态.被动收益.园丁.现有数>0">-->
      <!--        <el-tooltip-->
      <!--            :content="`花费${状态.自己种植.单价}个平菇来提升自己的工作效率!`"-->
      <!--            placement="top"-->
      <!--        >-->
      <!--          <el-button @click="提高种植效率" :disabled="状态.现有总数<状态.自己种植.单价">提升种植效率</el-button>-->
      <!--        </el-tooltip>-->
      <!--      </template>-->


    </div>
  </div>

  <!--    {{ 状态 }}-->

</template>
<script setup>
import {ref, reactive, computed, watch} from "vue";
import {CloseBold, InfoFilled, Male, Plus, QuestionFilled, Setting} from "@element-plus/icons-vue";
import {ElMessage} from "element-plus";
import vhCheck from "vh-check";

vhCheck('browser-address-bar')
const 状态 = ref({
  现有总数: 10000,
  最高总数: 0, //如果直接在这里用计算属性,那么会无限循环报错,所以用下面的watch
  总效率: computed(() => {
    let total = 0;
    Object.entries(状态.value.被动收益).forEach(([key, val]) => {
      total = total + val.总效率
      // console.log(key)
      // console.log(val)
    })
    return total
  }),
  自己种植: {
    效率: 1,
    单价: 50,
  },
  是否统一全球评估市场: false,
  被动收益: {
    园丁: {
      现有数: 0,
      单个效率: 0.1,//每秒每个总数应该增加多少
      单价: 10,// 每个园丁多少钱
      总效率: computed(() => {
        return 状态.value.被动收益.园丁.单个效率 * 状态.value.被动收益.园丁.现有数
      }),
      描述: '雇佣园丁来帮你种植!',
      购买按钮信息: '雇佣园丁',
      下次涨价: 1,
      是否展示: computed(() => {
        return 状态.value.现有总数 > 0
      }),
      立绘: "/园丁.png",
      背景: "https://img1.baidu.com/it/u=2338963158,2766763207&fm=253&fmt=auto&app=138&f=JPEG?w=750&h=500"
    },
    工业机器人: {
      现有数: 0,
      单个效率: 0.6,//每秒每个总数应该增加多少
      单价: 50,// 每个园丁多少钱
      总效率: computed(() => {
        return 状态.value.被动收益.工业机器人.单个效率 * 状态.value.被动收益.工业机器人.现有数
      }),
      描述: '购买工业机器人帮你种植!',
      购买按钮信息: '购买工业机器人',
      下次涨价: 5,
      是否展示: computed(() => {
        return 状态.value.被动收益.园丁.现有数 > 0
      }),
      立绘: "/机器人.png",
      背景: "https://q6.itc.cn/images01/20240311/342cf88af6984f9ea893a5e2b5934325.png"
    },
    腐木工厂: {
      现有数: 0,
      单个效率: 1,
      单价: 80,
      总效率: computed(() => {
        return 状态.value.被动收益.腐木工厂.单个效率 * 状态.value.被动收益.腐木工厂.现有数
      }),
      描述: '建设腐木工厂可以提高产量!',
      购买按钮信息: '建设腐木工厂',
      下次涨价: 8,
      是否展示: computed(() => {
        return 状态.value.被动收益.工业机器人.现有数 > 0
      }),
      立绘: "/腐木立绘.png",
      背景: "https://img2.baidu.com/it/u=629397067,750530828&fm=253&fmt=auto&app=138&f=JPEG?w=751&h=500"
    },
    从隔壁平菇院挖人: {
      现有数: 0,
      单个效率: 1.2,
      单价: 100,
      总效率: computed(() => {
        return 状态.value.被动收益.从隔壁平菇院挖人.单个效率 * 状态.value.被动收益.从隔壁平菇院挖人.现有数
      }),
      描述: '从隔壁平菇院挖人来帮我们种平菇!',
      购买按钮信息: '从隔壁平菇院挖人',
      下次涨价: 9,
      是否展示: computed(() => {
        return 状态.value.被动收益.腐木工厂.现有数 > 0
      }),
      立绘: "/平菇专家.png",
      背景: "https://img2.baidu.com/it/u=2286984080,2055483171&fm=253&fmt=auto&app=120&f=JPEG?w=801&h=500"

    },
    牛马打工人: {
      现有数: 0,
      单个效率: 1.3,
      单价: 110,
      总效率: computed(() => {
        return 状态.value.被动收益.牛马打工人.单个效率 * 状态.value.被动收益.牛马打工人.现有数
      }),
      描述: '花钱找中介公司压榨打工仔的生产力!他们比机器人还好用!',
      购买按钮信息: '招聘打工人',
      下次涨价: 10,
      是否展示: computed(() => {
        return 状态.value.被动收益.从隔壁平菇院挖人.现有数 > 0
      }),
      立绘: "/打工仔.png",
      背景: "https://photo.tuchong.com/21326148/f/1187581093.jpg"

    },
    椒鱼平菇联盟: {
      现有数: 0,
      单个效率: 2,
      单价: 150,
      总效率: computed(() => {
        return 状态.value.被动收益.椒鱼平菇联盟.单个效率 * 状态.value.被动收益.椒鱼平菇联盟.现有数
      }),
      描述: '组件椒鱼+平菇™联盟,会吸引更多的人来到我们平菇院',
      购买按钮信息: '组件椒鱼评估联盟',
      下次涨价: 12,
      是否展示: computed(() => {
        return 状态.value.被动收益.牛马打工人.现有数 > 0
      }),
      立绘: "/平菇联盟.png",
      背景: "https://img4.18183.com/uploads/allimg/171129/65-1G129115302.jpg@q_80"

    },
    月球殖民团: {
      现有数: 0,
      单个效率: 8,
      单价: 500,
      总效率: computed(() => {
        return 状态.value.被动收益.月球殖民团.单个效率 * 状态.value.被动收益.月球殖民团.现有数
      }),
      描述: '地球上的土地不够了!发射一些员工到月球去种植平菇!',
      购买按钮信息: '发射月球平菇殖民团!',
      下次涨价: 50,
      是否展示: computed(() => {
        return 状态.value.被动收益.椒鱼平菇联盟.现有数 > 0
      }),
      立绘: "/月球殖民团.png",
      背景: "https://img.cgmodel.com/image/2020/0303/cover/533070-2000137552.jpg"

    },
    平菇传送门: {
      现有数: 0,
      单个效率: 28,
      单价: 1500,
      总效率: computed(() => {
        return 状态.value.被动收益.平菇传送门.单个效率 * 状态.value.被动收益.平菇传送门.现有数
      }),
      描述: '建立传送门,窃取异世界的平菇!',
      购买按钮信息: '建立平菇传送门',
      下次涨价: 5,
      是否展示: computed(() => {
        return 状态.value.被动收益.月球殖民团.现有数 > 0
      }),
      立绘: "/传送门.png",
      背景: "https://img.hack6.com/tu/20211014/1633938045f82otk.jpg"
    },
  },

})
watch(状态.value, (新状态) => {
  状态.value.最高总数 = Math.max(新状态.现有总数, 新状态.最高总数)
})
setInterval(() => {
  状态.value.现有总数 = 状态.value.现有总数 + 状态.value.总效率
}, 1000)

function 统一全球评估市场() {
  状态.现有总数 = 状态.现有总数 - 500;
  Object.entries(状态.value.被动收益).forEach(([key, val]) => {
    val.单个效率 = val.单个效率 * 2
  })
  状态.value.是否统一全球评估市场 = true
}

function 购买(器械名, 器械) {
  console.log(`买${器械名}`)
  if (状态.value.现有总数 < 器械.单价) {
    ElMessage.error(`购买${器械名}需要${器械.单价}个平菇,你还差${状态.value.现有总数 - 器械.单价}`)
    return
  }
  状态.value.现有总数 = 状态.value.现有总数 - 器械.单价;
  器械.现有数 = 器械.现有数 + 1;
  器械.单价 = 器械.单价 + 器械.下次涨价
}

function 提高种植效率() {
  状态.value.自己种植.效率 = 状态.value.自己种植.效率 + 1;
  状态.现有总数 = 状态.现有总数 - 状态.value.自己种植.单价;
  状态.value.自己种植.单价 = 状态.value.自己种植.单价 + 50;
  ElMessage.success(`种植效率:${状态.value.自己种植.效率 - 1} --> ${状态.value.自己种植.效率}`)

}
</script>
<style scoped>
.主容器 {
  display: flex;
  flex-direction: column;
  height: 100vh;
  height: calc(100vh - var(--browser-address-bar, 0px));
}

.主页标题容器 {
  display: flex;
  flex-direction: column;
  background-image: url("https://pic.52112.com/180320/180320_66/IHacPhuVIn_small.jpg");
  background-size: cover
}

.主页内容容器 {
  /*background-image: url("https://2b.zol-img.com.cn/product/139/269/cedd3aQPoljU.jpg");*/
  /*background-size: cover;*/
  /*filter: brightness(150%) contrast(120%) grayscale(30%);*/
  /*filter: ;*/
}

.主页内容容器::before {
  /*content: '123';*/
  /*background: chocolate;*/
  /*z-index: 0; !* 将伪元素置于底层 *!*/
  /*background-image: url("https://2b.zol-img.com.cn/product/139/269/cedd3aQPoljU.jpg");*/
  /*background-size: cover;*/
}

.字体颜色 {
  color: white;
}

.被动收益 {
  display: flex;
  flex-direction: column;
  /*height: 20%;*/
  height: 6vh
}

.被动收益标题 {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  background-color: rgba(12, 12, 35, 0.6);

}

.被动收益内容 {
  display: flex;
  flex-direction: row;
  /*position: relative;*/
  flex-grow: 1;
  /*background-color: coral;*/
  height: 7vh;
  background-size: cover;

}

.被动收益图标 {
  width: 1.5em;
  height: 1.5em;
  background-color: rgba(0, 0, 0, 0.5);
  color: white;
  /*position: absolute;*/
  /*left: 50%;*/
  /*top: 50%;*/
  /*right: 0;*/
  /*transform: translate(-50%, -50%);*/
  border-radius: 50%
}

/*.被动收益加号 {*/
/*  width: 1.5em;*/
/*  height: 1.5em;*/
/*  background-color: rgba(0, 0, 0, 0.5);*/
/*  color: white;*/
/*  position: absolute;*/
/*  !*left: 50%;*!*/
/*  top: 50%;*/
/*  right: 0;*/
/*  transform: translate(-50%, -50%);*/
/*  border-radius: 50%*/
/*}*/
</style>