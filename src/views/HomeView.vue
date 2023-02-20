<template>
  <div>
    <h1 class="title">TV shows</h1>
    <div class="search-bar">
      <input v-model="searchInputData" placeholder="🔎 search a show"/>
    </div>
    <div v-for="[key, category] of Object.entries(filteredShows)" :key="key">
      <h2>{{ key }}</h2>
      <swiper
        :slides-per-view="5"
        :space-between="10"
        navigation
        :freeMode="true"
      >
        <swiper-slide
          v-for="show of category"
          :key="show.id"
          @click="handleClick(show.id)"
        >
          <div class="show-card">
            <img :src="show.image.medium">
            <h4>{{ show.name }}</h4>
            {{ show.rating.average || ' - ' }}⭐
          </div>
        </swiper-slide>
      </swiper>
    </div>
  </div>
</template>

<script>
import SwiperCore, { Navigation } from "swiper";
import { Swiper, SwiperSlide } from "swiper/vue";
import "swiper/swiper.css";
import "swiper/css/navigation";

// install Swiper components
SwiperCore.use([Navigation]);

export default {
  name: "ShowsView",
  components: {
    Swiper,
    SwiperSlide,
  },
  data() {
    return {
      tvShows: null,
      filteredShows: [],
      sortedShowsByRating: [],
      searchInputData: "",
    };
  },
  methods: {
    createCategories() {
      let showsCategories = {};
      this.tvShows.forEach((show) => {
        show.genres.forEach((genre) => {
          showsCategories[genre]
            ? showsCategories[genre].push(show)
            : (showsCategories = { ...showsCategories, [`${genre}`]: [show] });
        });
      });
      Object.values(showsCategories).forEach((entry) => {
        entry.sort((a, b) => b.rating.average - a.rating.average);
      });
      return showsCategories;
    },
    handleClick(showId) {
      this.$router.push({ name: "showDetails",params: {id: showId} });
    },
  },
  async mounted() {
    const url = "https://api.tvmaze.com/shows";

    await fetch(url)
      .then((res) => {
        if (!res.ok) throw new Error("shows could not be fetched");
        return res.json();
      })
      .then((data) => (this.tvShows = data))
      .catch((err) => console.warn(err));

    this.filteredShows = this.createCategories();
  },
};
</script>

<style scoped>
.title {
  text-align: center;
}

input {
  border: 2px solid aliceblue;
  border-radius: 10px;
}

.show-card {
  text-align: center;
  min-width: 210px;
  margin-bottom: 40px;
}

.show-card:hover {
  cursor: pointer;
}
</style>
