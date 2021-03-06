<template>
  <div class="balance-wrapper">
    <div
      :class="[
        'balance-container',
        title === balanceTitles.earnings
          ? 'earnings-container-height'
          : 'border-bottom'
      ]"
    >
      <div class="title-container">
        <p class="title">{{ title }}</p>
        <span v-if="title === balanceTitles.earnings" class="key-container">
          <div class="apr-circle"></div>
          <span>{{ $t('dappsAave.apy') }}</span>
          <div class="total-circle"></div>
          <span>{{ $t('dappsAave.total') }}</span>
        </span>
      </div>
      <i v-show="loadingHome" class="fa fa-spinner fa-spin" />
      <span v-if="title !== balanceTitles.earnings && !loadingHome">
        <p class="balance">${{ convertToFixed(balanceUsd) }}</p>
        <p class="eth-balance">
          {{ convertToFixed(balanceEth, '', 6) }}
          {{ $t('common.currency.eth') }}
        </p>
      </span>
      <div v-if="title === balanceTitles.earnings" class="earnings-container">
        <p v-if="earningsBalance === 0" class="no-data">
          {{ $t('dappsAave.no-data') }}
        </p>
      </div>
    </div>
    <div
      v-if="title !== balanceTitles.earnings && !loadingHome"
      class="composition-container"
    >
      <div class="row">
        <span class="title">{{ $t('dappsAave.composition') }}</span>
        <div
          v-if="
            title !== balanceTitles.aggregatedBal &&
              title !== balanceTitles.collateral
          "
        >
          <span class="percentage"
            >{{ percentageLeft ? percentageLeft : 100 }}%
          </span>
          <span class="available">{{ $t('dappsAave.available') }}</span>
        </div>
        <div v-if="composition.length === 0" class="composition-bar"></div>
        <div v-if="composition.length > 0" class="composition-wrap">
          <div
            v-for="(comp, idx) in composition"
            :key="idx"
            :class="[
              'composition-percentage-hover',
              idx === 0 ? 'left-border-radius' : '',
              idx === composition.length - 1 ? 'right-border-radius' : ''
            ]"
            :style="{ width: comp.percentage + '%', background: comp.color }"
            :title="
              comp.symbol +
                ' ' +
                convertToFixed(comp.amount, 'percentageLeft') +
                ' ' +
                comp.percentage +
                '%'
            "
          ></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import BigNumber from 'bignumber.js';

export default {
  props: {
    percentageLeft: {
      type: String,
      default: ''
    },
    title: {
      type: String,
      default: ''
    },
    balanceEth: {
      type: String,
      default: ''
    },
    balanceUsd: {
      type: String,
      default: ''
    },
    composition: {
      type: Array,
      default: () => {
        return [];
      }
    },
    earningsBalance: {
      type: Number,
      default: 0
    },
    loadingHome: {
      type: Boolean,
      default: true
    }
  },
  data() {
    return {
      compositionStyle: '',
      balanceTitles: {
        earnings: 'Earnings',
        collateral: 'Your Collateral',
        aggregatedBal: 'Aggregated Balance',
        borrowed: 'You Borrowed'
      }
    };
  },
  watch: {
    composition(newVal) {
      newVal.forEach((item, idx) => {
        if (item.percentage < 1) {
          newVal.splice(idx, 1);
        }
      });
    }
  },
  methods: {
    convertToFixed(val, type, num) {
      if (type === 'percentageLeft') {
        return '';
      }

      if (!val || val == 0) {
        return 0;
      }

      if (!num) {
        num = 2;
      }

      return new BigNumber(val).toFixed(num).toString();
    }
  }
};
</script>

<style lang="scss" scoped>
@import 'BalanceDisplay.scss';
</style>
