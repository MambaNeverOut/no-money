<template>
  <svg class="icon" @click="$emit('click', $event)" :class="name">
    <use :xlink:href="'#' + name" />
  </svg>
</template>

<script lang="ts">
let importAll = (requireContext: __WebpackModuleApi.RequireContext) =>
  requireContext.keys().forEach(requireContext);
try {
  importAll(require.context("../assets/icons", true, /\.svg$/));
} catch (error) {
  throw new Error(error);
}
export default {
  props: ["name"],
  name: "Icon"
};
</script>

<style lang="scss" scoped>
.icon {
  width: 1em;
  height: 1em;
  vertical-align: -0.15em;
  fill: currentColor;
  overflow: hidden;
}
.icon.traffic {
  color: #a7e566;
}
.icon.shopping {
  color: #ffd1ae;
}
.icon.food {
  color: #feecbe;
}
.icon.social {
  color: #c2a0ff;
}
.icon.rent {
  color: #ffdfc0;
}
.icon.medical {
  color: #a8c2ff;
}
.icon.play {
  color: #88eac6;
}
.icon.daily-consume {
  color: #ecccfe;
}
.icon.water-electricity {
  color: #64d4ea;
}
.icon.communication {
  color: #504fac;
}
//
.icon.pay {
  color: #88eac6;
}
.icon.part-time {
  color: #ff8383;
}
.icon.gift-money {
  color: #ffbe6a;
}
.icon.reimbursement {
  color: #ffb3b3;
}
.icon.bonus {
  color: #ffcfc1;
}
.icon.investment {
  color: #a9ceff;
}
</style>
