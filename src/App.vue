<template>
  <div class="主容器">

    <!--  标题和平菇图片-->
    <div>
      <div class="主页标题容器">
        <el-text class="字体颜色" style="font-size: 1.5em">我的平菇大院</el-text>
        <el-text class="字体颜色" size="large">院子里共有 {{ 状态.现有总数.toFixed(1) }} 个平菇</el-text>
        <el-text class="字体颜色">{{ 状态.总效率.toFixed(1) }} 个/秒</el-text>
      </div>
      <div class="主页内容容器" style="position: relative;text-align: center">
        <el-image
            style="width:100%;height:100%;position: absolute;left: 0;top: 0;filter: brightness(100%) contrast(100%) grayscale(30%);"
            src="/主页/主页背景图.jpg"></el-image>
        <el-image style="height: 25vh;" src="/主页/平菇.webp"></el-image>
        <Plus style="width: 3em;background-color: rgba(0,0,0,0.5);
      color: white;position: absolute;left: 50%;top: 50%;
      transform: translate(-50%,-50%);border-radius: 50%"
              @click="状态.现有总数=状态.现有总数+状态.自己种植.效率"></Plus>
      </div>
    </div>

    <!--    收益器材-->
    <div
        style="display: flex;flex-direction: column;flex-grow: 1;overflow-y: auto;background-image: url('/主页/主页标题背景.jpg');">
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
                <!--                <el-icon class="被动收益图标" @click="购买(key,val)">-->
                <!--                  <Plus></Plus>-->
                <!--                </el-icon>-->
                <el-icon class="被动收益图标" @click="展示器材对话=true;当前器材 = {器材名:key,器材:val}">
                  <Plus></Plus>
                </el-icon>
              </div>
            </div>
          </template>
        </div>
      </template>
    </div>

    <!--    底部按钮-->
    <div style="display: flex;justify-content: space-around;background-image: url('/主页/主页标题背景.jpg')">
      <!--      提升种植效率-->
      <div v-if="状态.被动收益.园丁.现有数>0" style="display: flex;flex-direction: row;align-items: center">
        <el-button style="background:rgba(51,11,11,0.75)" type="info" @click="展示提升效率对话=true">提升效率
        </el-button>
      </div>

      <!--      统一全球平菇市场-->
      <div v-if="状态.最高总数>500">
        <el-button style="background:rgba(51,11,11,0.75)" type="info" @click="展示统一市场对话=true">统一全球平菇市场
        </el-button>
      </div>

      <!--      通关！-->
      <div v-if="状态.最高总数>1000">
        <el-button style="background:rgba(51,11,11,0.75)" type="info" @click="展示建立平菇文明对话=true">
          建立平菇文明！
        </el-button>
      </div>


    </div>
  </div>

  <!-- 购买器材的对话 -->
  <el-dialog
      v-model="展示器材对话"
      width="95%"
  >
    <template #header>
      <div style="display: flex;flex-direction: row;justify-content: space-between">
        {{ 当前器材.器材名 }}
        <!--        <el-button type="primary" size="small" plain @click="购买(当前器材.器材名,当前器材.器材) ">-->
        <!--          {{ 当前器材.器材.购买按钮信息 }}-->
        <!--        </el-button>-->
      </div>
    </template>
    <template #default>
      <div style="display: flex;flex-direction: column">
        <div class="被动收益对话文字">
          <el-icon>
            <InfoFilled/>
          </el-icon>
          <el-text>{{ 当前器材.器材.描述 }}</el-text>
        </div>
        <div class="被动收益对话文字">
          <el-icon>
            <User/>
          </el-icon>
          <el-text>您共有<span style="color: forestgreen;font-size: 1.3em">{{ 当前器材.器材.现有数 }}</span>
            个{{ 当前器材.器材名 }}，每秒收获
            <span style="color: forestgreen;font-size: 1.3em">{{ 当前器材.器材.总效率.toFixed(1) }}</span>个平菇
          </el-text>
        </div>
        <div class="被动收益对话文字">
          <el-icon>
            <Coin/>
          </el-icon>
          <el-text>当前资费: <span style="font-size: 1.3em"
                                   :style="状态.现有总数>当前器材.器材.单价? 'color: forestgreen' : 'color: red'">{{
              当前器材.器材.单价
            }}</span> 平菇/每个
          </el-text>
        </div>
      </div>

      <div
          :style="`display: flex;flex-direction: row;background-image: url(${当前器材.器材.背景}); background-size: cover;height: 7vh`">
        <div
            :style="`display: flex;flex-grow: 1;flex-shrink: 1;` ">
          <el-image style="max-height: 100%;" fit="scale-down" :src="当前器材.器材.立绘"
                    v-for="i in Math.min(当前器材.器材.现有数,15)"></el-image>
        </div>
        <div v-if="当前器材.器材.现有数>15" style="display: flex;align-items: center">
          <!--                <el-text>-->
          <el-icon class="字体颜色">
            <CloseBold/>
          </el-icon>
          <!--                </el-text>-->
          <el-text class="字体颜色">{{ 当前器材.器材.现有数 }}</el-text>
        </div>
      </div>

      <div style="display: flex;flex-direction: column;align-items: center">
        <el-image style="width: 25%;" :src="当前器材.器材.立绘"></el-image>
        <el-button type="primary" size="small" plain @click="购买(当前器材.器材名,当前器材.器材) ">
          {{ 当前器材.器材.购买按钮信息 }}
        </el-button>
      </div>

    </template>
    <template #footer>
    </template>
  </el-dialog>

  <!--  提升效率的对话框 -->
  <el-dialog
      v-model="展示提升效率对话"
      width="95%"
  >
    <template #header>
      <div style="display: flex;flex-direction: row;justify-content: space-between">
        提升效率
        <el-button type="primary" size="small" plain @click="提高种植效率" :disabled="状态.现有总数<状态.自己种植.单价">
          提高种植效率
        </el-button>
      </div>
    </template>
    <template #default>
      <div style="display: flex;flex-direction: column">
        <div class="被动收益对话文字">
          <el-icon>
            <InfoFilled/>
          </el-icon>
          <el-text> 您可以提升自己手动种植平菇的效率</el-text>
        </div>
        <div class="被动收益对话文字">
          <el-icon>
            <User/>
          </el-icon>
          <el-text>您当前的效率为: {{ 状态.自己种植.效率 }}个平菇/每次
          </el-text>
        </div>
        <div class="被动收益对话文字">
          <el-icon>
            <Coin/>
          </el-icon>
          <el-text>培训所需资费: {{ 状态.自己种植.单价 }} 平菇/每次</el-text>
        </div>
      </div>
    </template>
    <template #footer>
    </template>
  </el-dialog>

  <!--  统一市场的 对话框-->
  <el-dialog
      v-model="展示统一市场对话"
      width="95%"
  >
    <template #header>
      <div style="display: flex;flex-direction: row;justify-content: space-between">
        统一市场
        <el-button type="primary" size="small" plain @click="统一全球评估市场"
                   :disabled="状态.统一市场.是否统一全球评估市场">
          {{ 状态.统一市场.是否统一全球评估市场 ? '已统一' : '我要统一市场!' }}
        </el-button>
      </div>
    </template>
    <template #default>
      <div style="display: flex;flex-direction: column">
        <div class="被动收益对话文字">
          <el-icon>
            <InfoFilled/>
          </el-icon>
          <el-text> 花大价钱统一全球平菇市场,您平菇院的效率永久翻倍!</el-text>
        </div>
        <div class="被动收益对话文字">
          <el-icon>
            <User/>
          </el-icon>
          <el-text>{{
              状态.统一市场.是否统一全球评估市场 ? '恭喜!您已统一全球平菇市场!平菇院的产量更高了!' : '您还没有统一全球平菇市场!加油啊!'
            }}
          </el-text>
        </div>
        <div class="被动收益对话文字">
          <el-icon>
            <Coin/>
          </el-icon>
          <el-text>{{
              状态.统一市场.是否统一全球评估市场 ? '你已经统一市场' : `统一市场需要${状态.统一市场.花费}个平菇`
            }}
          </el-text>
        </div>
      </div>

    </template>
    <template #footer>
    </template>
  </el-dialog>

  <!--  建立平菇文明的对话框-->
  <el-dialog
      v-model="展示建立平菇文明对话"
      width="95%"
  >
    <template #header>
      <div style="display: flex;flex-direction: row;justify-content: space-between">
        建立平菇文明!
        <el-button type="primary" size="small" plain @click="建立平菇文明"
                   :disabled="状态.建立平菇文明.是否建立平菇文明">
          {{ 状态.建立平菇文明.是否建立平菇文明 ? "你赢了" : "我要建立平菇文明!" }}
        </el-button>
      </div>
    </template>
    <template #default>
      <div style="display: flex;flex-direction: column">
        <div class="被动收益对话文字">
          <el-icon>
            <InfoFilled/>
          </el-icon>
          <el-text>
            举全平菇院之力,向宇宙各地发射平菇殖民舰队,建立一个统一的、辉煌的、至高无上的平菇文明！整个宇宙都会充满平菇！
          </el-text>
        </div>
        <div class="被动收益对话文字">
          <el-icon>
            <User/>
          </el-icon>
          <el-text>{{
              状态.建立平菇文明.是否建立平菇文明 ? '现在，整个宇宙都是您平菇大院的了！' : '宇宙中还有其他势力，加油啊！'
            }}
          </el-text>
        </div>
        <div class="被动收益对话文字">
          <el-icon>
            <Coin/>
          </el-icon>
          <el-text>{{
              状态.建立平菇文明.是否建立平菇文明 ? '你已经拥有了整个可见宇宙' : `统一市场需要${状态.建立平菇文明.花费}个平菇`
            }}
          </el-text>
        </div>
      </div>

    </template>
    <template #footer>
    </template>
  </el-dialog>

  <!--    {{ 状态 }}-->

</template>
<script setup>
import {ref, reactive, computed, watch} from "vue";
import {CloseBold, Coin, InfoFilled, Male, Plus, QuestionFilled, Setting, User} from "@element-plus/icons-vue";
import {ElMessage} from "element-plus";
import vhCheck from "vh-check";

vhCheck('browser-address-bar')
const 状态 = ref({
  现有总数: 0,
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
  统一市场: {
    是否统一全球评估市场: false,
    花费: 500,
  },
  建立平菇文明: {
    是否建立平菇文明: false,
    花费: 2000,
  },

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
      立绘: "/被动收益/园丁.png",
      背景: "/被动收益/园丁背景.jpg"
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
      立绘: "/被动收益/机器人.png",
      背景: "/被动收益/机器人背景.jpg"
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
      立绘: "/被动收益/腐木.png",
      背景: "/被动收益/腐木背景.jpg"
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
      立绘: "/被动收益/隔壁平菇院.png",
      背景: "/被动收益/隔壁评估院背景.jpg"

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
      立绘: "/被动收益/打工仔.png",
      背景: "/被动收益/打工仔背景.jpg"

    },
    椒鱼平菇联盟: {
      现有数: 0,
      单个效率: 2,
      单价: 150,
      总效率: computed(() => {
        return 状态.value.被动收益.椒鱼平菇联盟.单个效率 * 状态.value.被动收益.椒鱼平菇联盟.现有数
      }),
      描述: '组建椒鱼+平菇™联盟,会吸引更多的人来到我们平菇院',
      购买按钮信息: '组件椒鱼评估联盟',
      下次涨价: 12,
      是否展示: computed(() => {
        return 状态.value.被动收益.牛马打工人.现有数 > 0
      }),
      立绘: "/被动收益/平菇联盟.png",
      背景: "/被动收益/平菇联盟背景.jpg"

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
      立绘: "/被动收益/月球殖民团.png",
      背景: "/被动收益/月球殖民团背景.jpg"

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
      立绘: "/被动收益/传送门.png",
      背景: "/被动收益/传送门背景.jpg"
    },
  },

})
watch(状态.value, (新状态) => {
  状态.value.最高总数 = Math.max(新状态.现有总数, 新状态.最高总数)
})
setInterval(() => {
  状态.value.现有总数 = 状态.value.现有总数 + 状态.value.总效率
}, 1000)

const 当前器材 = ref()
const 展示器材对话 = ref(false)

const 展示提升效率对话 = ref(false)

const 展示统一市场对话 = ref(false)

const 展示建立平菇文明对话 = ref(false)


function 购买(器械名, 器械) {
  console.log(`买${器械名}`)
  if (状态.value.现有总数 < 器械.单价) {
    ElMessage.error(`购买${器械名}需要${器械.单价}个平菇,你还差${(状态.value.现有总数 - 器械.单价).toFixed(1)}`)
    return
  }
  状态.value.现有总数 = 状态.value.现有总数 - 器械.单价;
  器械.现有数 = 器械.现有数 + 1;
  器械.单价 = 器械.单价 + 器械.下次涨价
}

function 提高种植效率() {
  状态.value.自己种植.效率 = 状态.value.自己种植.效率 + 1;
  状态.value.现有总数 = 状态.value.现有总数 - 状态.value.自己种植.单价;
  状态.value.自己种植.单价 = 状态.value.自己种植.单价 + 50;
  ElMessage.success(`种植效率:${状态.value.自己种植.效率 - 1} --> ${状态.value.自己种植.效率}`)

}

function 统一全球评估市场() {
  状态.value.现有总数 = 状态.value.现有总数 - 状态.value.统一市场.花费;
  Object.entries(状态.value.被动收益).forEach(([key, val]) => {
    val.单个效率 = val.单个效率 * 2
  })
  状态.value.统一市场.是否统一全球评估市场 = true
}

function 建立平菇文明() {
  状态.value.现有总数 = 状态.value.现有总数 - 状态.value.建立平菇文明.花费;
  状态.value.建立平菇文明.是否建立平菇文明 = true

  console.log('你已经建立了平菇文明')
}

// 测试
状态.value.现有总数 = 1000
Object.entries(状态.value.被动收益).forEach(([key, val]) => {
  val.现有数 = 5
})


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
  background-image: url("/主页/主页标题背景.jpg");
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
  width: 1.8em;
  height: 1.8em;
  background-color: rgba(0, 0, 0, 0.5);
  color: white;
  /*position: absolute;*/
  /*left: 50%;*/
  /*top: 50%;*/
  /*right: 0;*/
  /*transform: translate(-50%, -50%);*/
  border-radius: 50%
}

.被动收益对话文字 {
  display: flex;
  flex-direction: row;
  align-items: center;
  margin-bottom: 1em;
}
</style>