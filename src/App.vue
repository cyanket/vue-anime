<template>
  <div>
    <div class="top-section">
      <h1>Search Anime Characters</h1>
      <div class="search-container">
        <i class="fa fa-search search-icon"/>
        <input class="search-input" v-model="search" @input="debouncedSearch" placeholder="Search for characters..." />
      </div>
      <p>Total {{ totalResults }} matching anime characters found</p>
    </div>
    <div id="bottom-section">
      <div v-if="characters.length == 0" class="warning">No results found!</div>
      <div v-else class="character-list">
        <div v-for="character in characters" :key="character.mal_id" class="character-item">
          <img width="50" height="50" :src="character.images.jpg.image_url">
          <div class="name-nick">

            <span class="names">{{ character.name }}</span>
            <span class="likes">{{ character.favorites }}</span>
            <i class="fa fa-heart likes" style="font-size:20px;color:red"/>
            <div v-if="character.nicknames" class="nickname-container">
              <span v-for="nickname in character.nicknames" :key="nickname" class="nickname-card">{{ nickname }}</span>
            </div>
          </div>
          <div class="link">
            <a :href="character.url" target="_blank">
              <i class="fa fa-arrow-right likes" style="font-size:50px;color:#9337d1d3"/>
            </a>
          </div>
        </div>
        <div class="pagination">
          <button @click="prevPage" :disabled="page == 1" class="page-button">Back</button>
          <button @click="nextPage" class="page-button">Next</button>
        </div>
      </div>
    </div>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
  </div>
</template>
<script>
import axios from 'axios';
import _ from 'lodash';

export default {
  data() {
    return {
      search: '',
      page: 1,
      totalResults: 0,
      characters: [],
    }
  },
  methods: {
    searchCharacters() {
      axios.get(`https://api.jikan.moe/v4/characters?page=${this.page}&limit=15&q=${this.search}&order_by=favorites&sort=desc`)
        .then(res => {
          this.characters = res.data.data;
          this.totalResults = res.data.pagination.items.total;
        })
    },
    nextPage() {
      this.page++;
      this.searchCharacters();
    },
    prevPage() {
      if (this.page > 1) {
        this.page--;
        this.searchCharacters();
      }
    },
  },
  mounted() {
    this.debouncedSearch = _.debounce(this.searchCharacters, 500);
    this.searchCharacters();
  },
}
</script>

<style>
body {
  background-color: #b57fe42e;
}

h1 {
  color: #333;
  text-align: center;
}

.top-section {
  font-family: cursive;
  background: url(https://wallpapercave.com/dwp2x/wp4779067.jpg);
  padding: 1rem 1rem 4rem 1rem;
  text-align: center;
  margin: -8px -8px 0 -8px;
}

#bottom-section {
  font-family: cursive;
  margin: 0 auto;
  padding: 20px;
  box-sizing: border-box;
  border-radius: 5px;
}


.search-input {
  width: 40%;
  padding: 10px 15px;
  font-size: 16px;
  border-radius: 15px;
  border: 1px solid #ccc;
  padding-left: 30px;
}

.search-container {
  position: relative;
  align-items: center;
}

.search-icon {
  position: relative;
  left: 25px;
}

.character-list {
  margin-top: 20px;
}

.nickname-container {
  margin-top: 10px;
  margin-left: 12px;
}

.nickname-card {
  background-color: #e7dcdcdc;
  padding: 5px;
  border-radius: 5px;
  margin-bottom: 5px;
  width: fit-content;
  margin-inline: .2rem;
  font-size: small;
}

.likes {
  float: right;
  padding-right: 8px;
}
.character-item {
  padding: 10px;
  background: #f5f2f2;
  border-radius: 5px;
  margin: 10px 0;
  border: 2px solid;
}

.names {
  margin-left: 1rem;
}

.link {
  position: absolute;
  display: -webkit-inline-box;
  right: 30px;
  border-left: 2px solid black;
  padding-inline: 5px;
}

.name-nick {
  position: absolute;
  display: inline-block;
  width: 85%;
  @media screen and (max-width: 450px) {
    width: 50%;
  }
}
.warning {
  color: red;
  font-weight: bold;
  text-align: center;
}

.pagination {
  display: flex;
  justify-content: space-between;
  margin-top: 20px;
}

.page-button {
  padding: 10px 20px;
  background-color: #f1f1f1;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
  border-style: groove;
}

.page-button:disabled {
  background-color: #ccc;
}
</style>
