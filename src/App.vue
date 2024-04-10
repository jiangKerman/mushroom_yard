<template>
  <el-text>欢迎来到平菇院，种植你的专属平菇</el-text>
  <el-divider></el-divider>
  <el-text>院子里共有 {{ 状态.现有总数 }} 个平菇</el-text>

  <el-divider></el-divider>
  <el-button @click="状态.现有总数=状态.现有总数+状态.自己种植.效率">种平菇</el-button>
  <el-row>
    <template v-if="状态.最高总数>=1">
      <el-tooltip
          :content="`花费${状态.园丁.单价}个平菇来雇佣园丁为你工作!`"
          placement="top"
      >
        <el-button @click=" 状态.现有总数=状态.现有总数-状态.园丁.单价;
                            状态.园丁.现有数=状态.园丁.现有数+1;
                            状态.园丁.单价=状态.园丁.单价+1"

                   :disabled="状态.现有总数<状态.园丁.单价">雇佣园丁
        </el-button>
      </el-tooltip>
      <el-text>园丁: {{ 状态.园丁.现有数 }}个 &nbsp;&nbsp; 总效率: {{ 状态.园丁.总效率 }} 个平菇每秒</el-text>
    </template>
  </el-row>
  <el-row>
    <template v-if="状态.园丁.现有数>0">
      <el-tooltip
          :content="`花费${状态.工业机器人.单价}个平菇来购买工业机器人为你种植!`"
          placement="top"
      >
        <el-button @click=" 状态.现有总数=状态.现有总数-状态.工业机器人.单价;
                            状态.工业机器人.现有数=状态.工业机器人.现有数+1;
                            状态.工业机器人.单价=状态.工业机器人.单价+5"

                   :disabled="状态.现有总数<状态.工业机器人.单价">购买工业机器人
        </el-button>
      </el-tooltip>
      <el-text>园丁: {{ 状态.工业机器人.现有数 }}个 &nbsp;&nbsp; 总效率: {{ 状态.工业机器人.总效率 }} 个平菇每秒
      </el-text>
    </template>
  </el-row>
  <el-divider></el-divider>
  <el-row>
    <template v-if="状态.园丁.现有数>0">
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

  {{ 状态 }}
  <!--  {{最高总数}}-->
</template>
<script setup>
import {ref, reactive, computed, watch} from "vue";

const 状态 = ref({
  现有总数: 0,
  最高总数: 0, //如果直接在这里用计算属性,那么会无限循环报错,所以用下面的watch
  总效率: computed(() => {
    return 状态.value.园丁.总效率 + 状态.value.工业机器人.总效率
  }),
  自己种植: {
    效率: 1,
    单价:50,
  },
  园丁: {
    现有数: 0,
    单个效率: 0.1,//每秒每个总数应该增加多少
    单价: 10,// 每个园丁多少钱
    总效率: computed(() => {
      return 状态.value.园丁.单个效率 * 状态.value.园丁.现有数
    })
  },
  工业机器人: {
    现有数: 0,
    单个效率: 0.6,//每秒每个总数应该增加多少
    单价: 50,// 每个园丁多少钱
    总效率: computed(() => {
      return 状态.value.工业机器人.单个效率 * 状态.value.工业机器人.现有数
    })
  },
  腐木工厂:{
    现有数:0,
    单个效率:1,
    单价:80,
    总效率: computed(() => {
      return 状态.value.腐木工厂.单个效率 * 状态.value.腐木工厂.现有数
    })
  }
})
watch(状态.value, (新状态) => {
  状态.value.最高总数 = Math.max(新状态.现有总数, 新状态.最高总数)
})
setInterval(() => {
  状态.value.现有总数 = 状态.value.现有总数 + 状态.value.总效率
}, 1000)
</script>