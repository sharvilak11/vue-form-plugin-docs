<template>
  <a :href="link" target="_blank" :class="size" title="Git-repository" aria-label="GitHub">
    <component :is="comp" class="icon" />
    {{text}}
  </a>
</template>

<script>
import { GithubIcon, GitlabIcon } from 'vue-feather-icons'

export default {
  components: {
    GitlabIcon,
    GithubIcon
  },
  props: {
    size: {
      type: String,
      default: 'small'
    },
    git: {
      type: String,
      default: 'GitHub'
    },
    text: {
      type: String,
      default: 'Check me out on GitHub'
    },
    link: {
      type: String
    }
  },
  computed: {
    comp() {
      return this.git === 'GitHub' ? GithubIcon : GitlabIcon
    }
  }
}
</script>

<style lang="scss" scoped>
a {
  display: inline-block;
  padding: 6px 10px;
  text-decoration: none;
  border-radius: 3px;
  font-size: 12px;
  line-height: 18px;
  font-weight: 700;
  transition: color .15s ease-in-out, background .15s ease-in-out;

  &:focus {
    outline: none;
  }

  .dark & {
    color: $textBright;
    background: $backgroundBright;
  }

  .bright & {
    color: $textDark;
    background: $backgroundDark;
  }

  svg {
    width: 18px;
    height: 18px;
    vertical-align: -6px;
    margin-right: 4px;
  }

  &.large {
    @include respond-above(sm) {
      font-size: 18px;
      padding: 12px 20px;
      line-height: 24px;

      svg {
        width: 24px;
        height: 24px;
      }
    }
  }
}
</style>

