<template>
  <dialog-container>
    <flickity-carousel ref="flickity" :options="flickityOptions">
      <div v-for="project in projects" class="carousel-cell">
        <h2>{{ project.title }}</h2>
        <p class="description">{{ project.description }}</p>
        <div class="video" v-bind:style="{ backgroundImage: `url(${project.poster})` }"></div>
      </div>
    </flickity-carousel>
    <button class="close" @click="closeDialog">Close</button>
  </dialog-container>
</template>

<script>
import FlickityCarousel from '@/components/elements/FlickityCarousel'
import Projects from '@/constants/projects'

export default {
  name: 'app-dialog',
  components: {
    FlickityCarousel
  },
  activated () {
    this.updateRoute()
    this.$refs.flickity.on('settle', this.updateRoute)
  },
  data () {
    return {
      projects: Projects,
      flickityOptions: {
        initialIndex: this.getProjectIndex(),
        prevNextButtons: false,
        pageDots: false
      }
    }
  },
  methods: {
    closeDialog () {
      this.$refs.flickity.off('settle', this.updateRoute)
      this.$router.push(this.$route.matched[0].path)
    },
    getProjectIndex () {
      const index = Projects.findIndex((prj) => prj.id === this.$route.params.slide)
      return index > -1 ? index : 0
    },
    getProjectName () {
      const index = this.$refs.flickity.selectedIndex() || 0
      return Projects[index].id
    },
    updateRoute () {
      if (!this.$route.params.slide) {
        this.$router.replace(`${this.$route.path}/${this.getProjectName()}`)
      } else {
        this.$router.replace(this.getProjectName())
      }
    }
  }
}
</script>

<style lang="sass" scoped>
@import ../styles/shared

dialog-container
  +fix(0 $headerHeight 0 0)
  display: block
  background-color: rgba(0,0,0,0.9)
  will-change: transform
  z-index: 1000
  color: #fff

  +media('<tablet')
    top: $headerHeightMobile

  .flickity-enabled
    +abs(left center)
    width: 100%
    height: 100%
    max-height: 70%

    .carousel-cell
      position: relative
      width: 100%
      max-width: $contentMaxWidth
      margin-right: 70px
      opacity: 0.25
      transition: opacity $transitionDuration linear

      &.is-selected
        opacity: 1

    .video
      +aspect-ratio(16, 9)
      background-position: 0 0
      background-repeat: no-repeat
      background-size: cover

.close
  +abs(40px)
  color: #fff

</style>
