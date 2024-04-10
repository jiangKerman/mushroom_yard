<template>
  <el-text>欢迎来到平菇院，种植你的专属平菇</el-text>
  <el-divider></el-divider>
  <el-text>院子里共有 {{ 状态.现有总数 }} 个平菇</el-text>

  <el-divider></el-divider>
  <el-button @click="状态.现有总数=状态.现有总数+状态.自己种植.效率">种平菇</el-button>
  <el-divider></el-divider>
  <el-row v-for="(val,key) in 状态.被动收益" :key="key">
    <template v-if="val.是否展示">
      <el-tooltip
          :content="`花费${val.单价}: ${val.描述}`"
          placement="top"
      >
        <el-button @click="状态.现有总数 = 状态.现有总数 - val.单价;
val.现有数=val.现有数+1;
val.单价=val.单价+val.下次涨价"
                   :disabled="状态.现有总数<val.单价">{{ val.购买按钮信息 }}
        </el-button>

      </el-tooltip>
      <el-text>{{ key }}: {{ val.现有数 }}个 &nbsp;&nbsp; 单个效率:{{ val.单个效率 }}&nbsp;&nbsp; 总效率: {{
          val.总效率
        }} 个平菇每秒
      </el-text>
    </template>

  </el-row>
  <!--  <el-row>-->
  <!--    <template v-if="状态.最高总数>=1">-->
  <!--      <el-tooltip-->
  <!--          :content="`花费${状态.园丁.单价}个平菇来雇佣园丁为你工作!`"-->
  <!--          placement="top"-->
  <!--      >-->
  <!--        <el-button @click=" 状态.现有总数=状态.现有总数-状态.园丁.单价;-->
  <!--                            状态.园丁.现有数=状态.园丁.现有数+1;-->
  <!--                            状态.园丁.单价=状态.园丁.单价+1"-->

  <!--                   :disabled="状态.现有总数<状态.园丁.单价">雇佣园丁-->
  <!--        </el-button>-->
  <!--      </el-tooltip>-->
  <!--      <el-text>园丁: {{ 状态.园丁.现有数 }}个 &nbsp;&nbsp; 总效率: {{ 状态.园丁.总效率 }} 个平菇每秒</el-text>-->
  <!--    </template>-->
  <!--  </el-row>-->
  <!--  <el-row>-->
  <!--    <template v-if="状态.园丁.现有数>0">-->
  <!--      <el-tooltip-->
  <!--          :content="`花费${状态.工业机器人.单价}个平菇来购买工业机器人为你种植!`"-->
  <!--          placement="top"-->
  <!--      >-->
  <!--        <el-button @click=" 状态.现有总数=状态.现有总数-状态.工业机器人.单价;-->
  <!--                            状态.工业机器人.现有数=状态.工业机器人.现有数+1;-->
  <!--                            状态.工业机器人.单价=状态.工业机器人.单价+5"-->

  <!--                   :disabled="状态.现有总数<状态.工业机器人.单价">购买工业机器人-->
  <!--        </el-button>-->
  <!--      </el-tooltip>-->
  <!--      <el-text>园丁: {{ 状态.工业机器人.现有数 }}个 &nbsp;&nbsp; 总效率: {{ 状态.工业机器人.总效率 }} 个平菇每秒-->
  <!--      </el-text>-->
  <!--    </template>-->
  <!--  </el-row>-->
  <el-divider></el-divider>
  <el-row>
    <template v-if="状态.被动收益.园丁.现有数>0">
      <el-tooltip
          :content="`花费${状态.自己种植.单价}个平菇来提升自己的工作效率!`"
          placement="top"
      >
        <el-button @click=" 状态.自己种植.效率=状态.自己种植.效率+1;
  状态.现有总数=状态.现有总数-状态.自己种植.单价;
  状态.自己种植.单价=状态.自己种植.单价+50;
  "
                   :disabled="状态.现有总数<状态.自己种植.单价">提升种植效率
        </el-button>
      </el-tooltip>
      <el-text>当前效率: {{ 状态.自己种植.效率 }}个/次</el-text>
    </template>
  </el-row>
  <el-row>
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
  </el-row>

  {{ 状态 }}
  <!--  {{最高总数}}-->
</template>
<script setup>
import {ref, reactive, computed, watch} from "vue";

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
      })
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
      })
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
      })
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
      })
    },
    流浪者团队: {
      现有数: 0,
      单个效率: 1.3,
      单价: 110,
      总效率: computed(() => {
        return 状态.value.被动收益.流浪者团队.单个效率 * 状态.value.被动收益.流浪者团队.现有数
      }),
      描述: '花钱找中介公司压榨流浪者的生产力!',
      购买按钮信息: '找中介公司发虚假广告',
      下次涨价: 10,
      是否展示: computed(() => {
        return 状态.value.被动收益.从隔壁平菇院挖人.现有数 > 0
      })
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
        return 状态.value.被动收益.流浪者团队.现有数 > 0
      })
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

</script>