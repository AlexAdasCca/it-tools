<script setup lang="ts">
import { compareSync, hashSync } from 'bcryptjs';
import { useThemeVars } from 'naive-ui';
import { useCopy } from '@/composable/copy';

const themeVars = useThemeVars();

const input = ref('');
const saltCount = ref(10);
const hashed = computed(() => hashSync(input.value, saltCount.value));

const { t } = useI18n()
const { copy } = useCopy({ source: hashed, text: t('tools.bcrypt-card.copy-tooltip') });

const compareString = ref('');
const compareHash = ref('');
const compareMatch = computed(() => compareSync(compareString.value, compareHash.value));
</script>

<template>
  <c-card :title="$s('tools.bcrypt-card.title')">
    <c-input-text
      v-model:value="input"
      :placeholder="$s('tools.bcrypt-card.in-bcrypt')"
      raw-text
      :label="$s('tools.bcrypt-card.label-string')"
      label-position="left"
      label-align="right"
      label-width="120px"
      mb-2
    />
    <n-form-item :label="$s('tools.bcrypt-card.label-salt-count')" label-placement="left" label-width="120">
      <n-input-number v-model:value="saltCount" :placeholder="$s('tools.bcrypt-card.label-salt-round')" :max="100" :min="0" w-full />
    </n-form-item>

    <c-input-text :value="hashed" readonly text-center />

    <div mt-5 flex justify-center>
      <c-button @click="copy()">
        {{ $s('tools.bcrypt-card.copy-button') }}
      </c-button>
    </div>
  </c-card>

  <c-card :title="$s('tools.bcrypt-card.comp-title')">
    <n-form label-width="120">
      <n-form-item :label="$s('tools.bcrypt-card.label-string')" label-placement="left">
        <c-input-text v-model:value="compareString" :placeholder="$s('tools.bcrypt-card.comp-string')" raw-text />
      </n-form-item>
      <n-form-item label="Your hash: " label-placement="left">
        <c-input-text v-model:value="compareHash" :placeholder="$s('tools.bcrypt-card.comp-hash')" raw-text />
      </n-form-item>
      <n-form-item :label="$s('tools.bcrypt-card.hash-matched')" label-placement="left" :show-feedback="false">
        <div class="compare-result" :class="{ positive: compareMatch }">
          {{ compareMatch ? 'Yes' : 'No' }}
        </div>
      </n-form-item>
    </n-form>
  </c-card>
</template>

<style lang="less" scoped>
.compare-result {
  color: v-bind('themeVars.errorColor');

  &.positive {
    color: v-bind('themeVars.successColor');
  }
}
</style>
