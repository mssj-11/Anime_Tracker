<script setup>
import { ref, computed, onMounted } from 'vue'

const query = ref('')
const my_anime = ref([])
const search_results = ([])

const my_anime_asc = computed(() => {
  return my_anime.value.sort((a, b) => {
    return a.title.localeCompare(b.title)
  })
})

const searchAnime = () => {
  const url = `https://api.jikan.moe/v4/anime?q=${query.value}`
  fetch(url)
      .then(response => response.json())
      .then(response => {
        search_results.value = response.data
      })
}

const handleInput = e => {
  if (!e.target.value) {
    search_results.value = []
  }
}

const addAnime = anime => {
  search_results.value = []
  query.value = ''

  my_anime.value.push({
    id: anime.mal_id,
    title: anime.title,
    episodes: anime.episodes,
    image: anime.image.jpg.image_url,
    watched_episodes: 0
  })

  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

const increaseWatch = anime => {
  anime.watched_episodes++
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

const decreaseWatch = anime => {
  anime.watched_episodes--
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

onMounted(() => {
  my_anime.value = JSON.parse(localStorage.getItem('my_anime')) || []
})
</script>

<template>
  <main>Helloo, World</main>
</template>

<style scoped>
</style>
