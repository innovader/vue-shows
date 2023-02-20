<template>
  <div class="">
    <div class="show-info">
      <h4>{{ showInfo?.name }}</h4>
      <img :src="showInfo?.image.medium">
      <p>Rating: {{ showInfo?.rating.average || ' - ' }}⭐</p>
      <p>Genres: {{showInfo?.genres}}</p>
      <p>Released: {{showInfo?.premiered}}</p>
      <p v-html=showInfo?.summary></p>
    </div>
  </div>
</template>

<script>
export default {
  name: "ShowDetails",
  data() {
    return {
      id: this.$route.params.id,
      showInfo: null,
    };
  },
  async mounted() {
    const url = `https://api.tvmaze.com/shows/${this.id}`;

    await fetch(url)
      .then((res) => {
        if (!res.ok) throw new Error("shows could not be fetched");
        return res.json();
      })
      .then((data) => (this.showInfo = data))
      .catch((err) => console.warn(err));

    this.filteredShows = this.createCategories();
  },
}
</script>

<style scoped>
.show-info {
  text-align: center;
}
</style>
