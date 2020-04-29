<template>
  <Layout :sidebar="false">
    <div class="content">
      <div class="logo-wrapper">
        <img class="logo" src="../assets/img/vue.png" alt="logo">
      </div>
      <h1>Vue Form Plugin - Build your forms dynamically.</h1>
      <nav>
        <!-- To use other icons here, you need to import them in the Shortcut component -->
        <Shortcut link="/getting-started" text="Getting Started" icon="play-icon" />
        <Shortcut link="/configuration" text="Configuration" icon="sliders-icon" />
        <Shortcut link="/examples" text="Examples" icon="eye-icon" />
      </nav>
      <GitLink class="git" size="large" link="https://github.com/sharvilak11/vue-form-plugin"  />
      <GitLink class="git git-lab" size="large" git="GitLab" link="http://git.e9ine.com/sharvilak/vue-form-plugin" text="Check me out on GitLab" />
    </div>
  </Layout>
</template>
<static-query>
  query {
    metadata {
      siteName
    }
  }
</static-query>
<script>
import GitLink from '~/components/GitLink.vue'
import Shortcut from '~/components/Shortcut.vue'

export default {
  components: {
    GitLink,
    Shortcut
  },
  data() {
    return {
      description: 'The ultimate form plugin to create forms dynamically'
    }
  },
  metaInfo() {
    return {
      title: this.description,
      meta: [
        { key: 'description', name: 'description', content: 'Documentation for vue-form-plugin created by @e9ine.' }
      ]
    }
  }
}
</script>

<style lang="scss" scoped>
.content {
  display: flex;
  flex-direction: column;
  .logo-wrapper {
    align-self: center;
  }
  .logo {
    height: 80px;
    width: 80px;
  }
}

h1 {
  text-align: center;
  max-width: 600px;
  margin: 1em auto 1.5em;

  @include respond-above(md) {
    max-width: 1000px;
  }
}

h2 {
  font-size: 1.4rem;
  margin: 0;
}

nav {
  display: flex;
  justify-content: space-between;
  flex-direction: column;

  @include respond-above(sm) {
    flex-direction: row;
  }
}

.git {
  margin: 2em 0 0;
  align-self: center;

  @include respond-above(md) {
    margin: 5em 0 0;
  }
  &-lab {
    background-color: #ff9f00 !important;
    margin: 1em;
  }
}
</style>
