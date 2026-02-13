<template>
  <section id="hero" class="hero">
    <div class="hero-inner">
      <h1>
        <span class="h1-top">{{ t.hero.line1 }}</span>
        <span class="h1-bot">{{ t.hero.line2 }}</span>
      </h1>

      <p class="hero-p">{{ t.hero.desc }}</p>

      <div class="hero-btns">
        <button class="btn btn-pri" @click="$emit('goto','submit')">
          {{ t.hero.cta }}
          <span class="btn-shine"></span>
        </button>
      </div>

      <div class="stats">
        <div class="stat" v-for="s in heroStats" :key="s.lbl">
          <div class="stat-n">{{ s.val }}</div>
          <div class="stat-l">{{ s.lbl }}</div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  props: {
    t: Object,
    heroStats: Array
  }
}
</script>
