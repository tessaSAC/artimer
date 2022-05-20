<template>
<el-config-provider namespace="ep">
  <el-container>
    <el-header>
      <BaseHeader />
    </el-header>
    <!-- <el-container>
      <el-aside>
        <BaseSide />
      </el-aside> -->

      <el-main>
        <PairGenerator />
      </el-main>
    <!-- </el-container> -->
  </el-container>
</el-config-provider>
</template>

<style>
#app {
  text-align: center;
  color: var(--ep-text-color-primary);
}

.element-plus-logo {
  width: 50%;
}
</style>
