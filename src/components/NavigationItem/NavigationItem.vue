<template>
  <span
    class="NavigationItem"
    :class="classes">

    <span
      v-if="showLabel"
      class="NavigationItem__label">{{ item.name }}</span>

    <router-link
      v-if="showRouterLink"
      :to="item.meta.target"
      class="NavigationItem__router-link">{{ item.name }}</router-link>

    <a
      v-if="showHyperLink"
      :href="item.meta.target"
      class="NavigationItem__link">{{ item.name }}</a>

    <a
      v-if="showExternalHyperLink"
      :href="item.meta.target"
      target="_blank"
      class="NavigationItem__external-link">{{ item.name }}</a>
  </span>
</template>

<script>
export default {
  data() {
    return {
      active: false,
      disabled: false,
    };
  },
  props: {
    item: Object,
    required: true,
  },
  methods: {
    isActive() {
      if (this.item.meta.target === '') {
        return false;
      }

      if (this.$route) {
        return (
          (this.$route.path + this.$route.hash).endsWith(
            this.item.meta.target
          ) ||
          (this.$route.path + this.$route.hash).endsWith(
            this.item.meta.target + '/'
          )
        );
      }

      return (
        window.location.href.endsWith(this.item.meta.target) ||
        window.location.href.endsWith(this.item.meta.target + '/')
      );
    },
    isDisabled() {
      return this.item.disabled;
    }
  },
  computed: {
    showLabel() {
      return (
        this.disabled ||
        this.item.path === undefined &&
        this.item.element === undefined &&
        this.item.external === undefined
      );
    },
    showRouterLink() {
      return this.showLink && this.$router !== undefined;
    },
    showHyperLink() {
      return this.showLink && this.$router === undefined;
    },
    showExternalHyperLink() {
      return this.item.external !== undefined && !this.disabled;
    },
    showLink() {
      return (this.item.path !== undefined || this.item.element !== undefined) && !this.disabled;
    },
    classes() {
      return {
        'NavigationItem--active': this.active,
        'NavigationItem--disabled': this.disabled,
      };
    },
  },
  watch: {
    item() {
      this.active = this.isActive();
      this.disabled = this.isDisabled();
    },
    $route() {
      this.active = this.isActive();
      this.disabled = this.isDisabled();
    },
  },
  mounted() {
    this.active = this.isActive();
    this.disabled = this.isDisabled();

    if (!this.$router) {
      window.addEventListener('hashchange', () => {
        this.active = this.isActive();
        this.disabled = this.isDisabled();
      });
    }
  },
};
</script>

<style lang="scss">
@import './NavigationItem.scss';
</style>
