<template>
  <div class="bg-slate-800 rounded-lg p-4 border border-slate-700">
    <div class="flex items-center justify-between mb-4">
      <h3 class="text-sm font-bold text-cyan-400">研究周报</h3>
      <span class="text-xs text-slate-500">{{ report.periodStart }} ~ {{ report.periodEnd }}</span>
    </div>

    <div class="grid grid-cols-2 gap-2 mb-4">
      <div class="bg-slate-900 rounded p-2 text-center">
        <div class="text-xs text-slate-500">总浏览</div>
        <div class="text-lg font-bold text-cyan-400">{{ report.browseCount }}</div>
      </div>
      <div class="bg-slate-900 rounded p-2 text-center">
        <div class="text-xs text-slate-500">唯一分子</div>
        <div class="text-lg font-bold text-green-400">{{ report.uniqueMolecules }}</div>
      </div>
      <div class="bg-slate-900 rounded p-2 text-center">
        <div class="text-xs text-slate-500">新增收藏</div>
        <div class="text-lg font-bold text-yellow-400">{{ report.favoriteCount }}</div>
      </div>
      <div class="bg-slate-900 rounded p-2 text-center">
        <div class="text-xs text-slate-500">高风险</div>
        <div class="text-lg font-bold text-red-400">{{ report.highRiskMolecules.length }}</div>
      </div>
    </div>

    <div class="mb-4">
      <h4 class="text-xs font-bold text-slate-400 mb-2">热门浏览 TOP5</h4>
      <div class="space-y-1">
        <div
          v-for="(item, index) in report.topBrowseMolecules"
          :key="item.molecule.id"
          @click="store.selectMolecule(item.molecule)"
          class="cursor-pointer flex items-center gap-2 bg-slate-900 rounded p-2 hover:bg-slate-700 transition"
        >
          <span class="w-5 h-5 flex items-center justify-center text-xs font-bold rounded" :class="index < 3 ? 'bg-cyan-500/20 text-cyan-400' : 'bg-slate-700 text-slate-400'">{{ index + 1 }}</span>
          <span class="text-sm text-slate-200 flex-1 truncate">{{ item.molecule.name }}</span>
          <span class="text-xs text-slate-500">{{ item.count }}次</span>
        </div>
        <div v-if="report.topBrowseMolecules.length === 0" class="text-xs text-slate-600 text-center py-2">暂无浏览数据</div>
      </div>
    </div>

    <div class="mb-4">
      <h4 class="text-xs font-bold text-slate-400 mb-2">高风险分子</h4>
      <div class="space-y-1 max-h-32 overflow-y-auto">
        <div
          v-for="mol in report.highRiskMolecules.slice(0, 5)"
          :key="mol.id"
          @click="store.selectMolecule(mol)"
          class="cursor-pointer flex items-center justify-between bg-slate-900 rounded p-2 hover:bg-slate-700 transition"
        >
          <span class="text-sm text-slate-200">{{ mol.name }}</span>
          <span class="text-xs text-red-400">高风险</span>
        </div>
        <div v-if="report.highRiskMolecules.length === 0" class="text-xs text-slate-600 text-center py-2">暂无高风险分子</div>
      </div>
    </div>

    <div class="mb-4">
      <h4 class="text-xs font-bold text-slate-400 mb-2">最近收藏</h4>
      <div class="space-y-1 max-h-32 overflow-y-auto">
        <div
          v-for="mol in report.recentFavorites.slice(0, 5)"
          :key="mol.id"
          @click="store.selectMolecule(mol)"
          class="cursor-pointer flex items-center justify-between bg-slate-900 rounded p-2 hover:bg-slate-700 transition"
        >
          <span class="text-sm text-slate-200">{{ mol.name }}</span>
          <span class="text-yellow-400 text-xs">★</span>
        </div>
        <div v-if="report.recentFavorites.length === 0" class="text-xs text-slate-600 text-center py-2">暂无收藏</div>
      </div>
    </div>

    <div>
      <h4 class="text-xs font-bold text-slate-400 mb-2">分类浏览统计</h4>
      <div class="space-y-1">
        <div v-for="stat in report.categoryStats" :key="stat.category" class="flex items-center gap-2">
          <span class="text-xs text-slate-400 w-20 truncate">{{ stat.category }}</span>
          <div class="flex-1 bg-slate-900 rounded-full h-2 overflow-hidden">
            <div
              class="h-full bg-cyan-500 rounded-full transition-all"
              :style="{ width: (stat.count / maxCategoryCount * 100) + '%' }"
            ></div>
          </div>
          <span class="text-xs text-slate-500 w-8 text-right">{{ stat.count }}</span>
        </div>
        <div v-if="report.categoryStats.length === 0" class="text-xs text-slate-600 text-center py-2">暂无统计数据</div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { useMoleculeStore } from '../store/molecule'

const store = useMoleculeStore()
const report = computed(() => store.weeklyReport)

const maxCategoryCount = computed(() => {
  const stats = report.value.categoryStats
  if (stats.length === 0) return 1
  return Math.max(...stats.map(s => s.count))
})
</script>
