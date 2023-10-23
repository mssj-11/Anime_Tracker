<script setup>
import { ref, computed, onMounted } from 'vue'

const query = ref('')
const my_anime = ref([])
const search_results = ref([])

const my_anime_asc = computed(() => {
  return my_anime.value.sort((a, b) => {
    return a.title.localeCompare(b.title)
  })
})

const searchAnime = () => {
  const url = `https://api.jikan.moe/v4/anime?q=${query.value}`
  fetch(url)
      .then(response => response.json())
      .then(data => {
        search_results.value = data.data
      })
}

const handleInput = (e) => {
  if (!e.target.value) {
    search_results.value = []
  }
}

const addAnime = (anime) => {
  search_results.value = []
  query.value = ''

  my_anime.value.push({
    id: anime.mal_id,
    title: anime.title,
    total_episodes: anime.episodes,
    image: anime.images.jpg.image_url,
    watched_episodes: 0,
  })

  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

const increaseWatch = (anime) => {
  anime.watched_episodes++
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

const decreaseWatch = (anime) => {
  anime.watched_episodes--
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

onMounted(() => {
  my_anime.value = JSON.parse(localStorage.getItem('my_anime')) || []
})
</script>

<template>
  <main>
    <h1>My Anime List</h1>

    <form @submit.prevent="searchAnime">
      <input type="text" placeholder="Search for an anime..." v-model="query" @input="handleInput" />
      <button type="submit" class="button">Search</button>
    </form>

    <div class="results" v-if="search_results.length > 0">
      <div class="result" v-for="anime in search_results">
        <img :src="anime.images.jpg.image_url" alt="image-anime" />
        <div class="details">
          <h3>{{ anime.title }}</h3>
          <p :title="anime.synopsis" v-if="anime.synopsis">{{ anime.synopsis.slice(0, 120) }}...</p>
          <span class="flex-1"></span>
          <button @click="addAnime(anime)" class="button">Add to My Anime</button>
        </div>
      </div>
    </div>

    <div class="myanime" v-if="my_anime.length > 0">
      <h2>My Anime</h2>
      <div class="anime" v-for="anime in my_anime_asc">
        <img :src="anime.image" alt="image-anime">
        <h3>{{ anime.title }}</h3>
        <div class="flex-1"></div>
        <span class="episodes">{{ anime.watched_episodes }} / {{ anime.total_episodes }}</span>
        <button class="button" v-if="anime.total_episodes !== anime.watched_episodes" @click="increaseWatch(anime)">+</button>
        <button class="button" v-if="anime.watched_episodes > 0" @click="decreaseWatch(anime)">-</button>
      </div>
    </div>
  </main>
</template>

<style scoped>
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Fira Sans', sans-serif;
}

body { background-color: #eee; }

main{
  margin: 0 auto;
  max-width: 768px;
  padding: 1.5rem;
}

h1{
  text-align: center;
  margin-bottom: 1.5rem;
  padding: .5rem;
  background-color: #49a52c;
  color: #fff;
}

form{
  display: flex;
  max-width: 480px;
  margin: 0 auto 1.5rem;
}

form input {
  appearance: none;
  outline: none;
  border: none;
  display: block;
  width: 100%;
  background: white;
  color: #888;
  font-size: 1.125rem;
  padding: .5rem 1rem;
  box-shadow: 0px 2px 4px rgba(0, 0, 0, .2);
}

.button{
  background: none;
  appearance: none;
  outline: none;
  border: none;
  display: block;
  padding: .5rem 1rem;
  background-image: linear-gradient(to right, #49a52c 50%, #2d7bd8 50%);
  background-size: 200%;
  color: white;
  font-size: 1rem;
  font-weight: bold;
  text-transform: uppercase;
  transition: .4s;
  cursor: pointer;
}
.button:hover{  background-position: right;  }

.results{
  background-color: #fff;
  border-radius: .5rem;
  margin-bottom: 1.5rem;
  box-shadow: 0px 2px 4px rgba(0, 0, 0, .5);
  max-height: 480px;
  overflow-y: scroll;
}

.result{
  display: flex;
  margin: 1rem;
  padding: 1rem;
  border: 1px solid #ccc;
  border-radius: 5px;
  transition: .4s;
}

.result img {
  widows: 100%;
  border-radius: 1rem;
  margin-right: 1rem;
}

.details {
  flex: 1 1 0%;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

.details h3 {
  font-size: 1.25rem;
  margin-bottom: .5rem;
}

.details p{
  font-size: .875rem;
  margin-bottom: 1rem;
}
.details .button{   margin-left: auto;  }

.flex-1{
  display: block;
  flex: 1 1 0%;
}

.myanime h2{
  color: #000;
  font-weight: bold;
  margin-bottom: 1.5rem;
}


.myanime .anime {
  display: flex;
  align-items: center;
  margin-bottom: 1.5rem;
  background-color: #fff;
  padding: 1rem;
  border-radius: .5rem;
  box-shadow: 0px 2px 4px rgba(0, 0, 0, .6);
}

.anime img {
  width: 72px;
  height: 72px;
  object-fit: cover;
  border-radius: 1rem;
  margin-right: 1rem;
}

.anime h3{
  color: #888;
  font-size: 1.125rem;
}

.anime .episodes{
  margin-right: 2rem;
  color: #888;
}

.anime .button:first-of-type{   margin-right: .5rem;  }
.anime .button:last-of-type{   margin-right: 0;  }

</style>