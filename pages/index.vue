<template>
  <div class="home">
    
    <TopBar />

    <!-- Search -->
    <div class="container search">
      <v-btn
      v-show="searchInput !== ''" 
      @click="clearSearch"
      depressed
      id="button"
      >
        Clear Search
      </v-btn>
      <v-text-field
        label="Search a movie"
        hide-details="auto"
        @keyup.enter="$fetch" 
        v-model.lazy="searchInput"
      ></v-text-field>
    </div>

    <!-- Loading Animation -->
    <Loading v-if="$fetchState.pending" />

    <!-- Movies -->
    <div v-else class="container movies">
      <!-- Search Results -->
      <div id="movie-grid" v-if="searchInput !== ''" class="movies-grid">
        <div class="movie" v-for="(movie, index) in searchedMovies" :key="index">
          <div class="movie-img">
            <img :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`" alt />
            <p class="review">{{ movie.vote_average }}</p>
            <p class="overview">{{ movie.overview }}</p>
          </div>
          <div class="info">
            <p class="title">
              {{
                movie.title.slice(0, 25)
              }}
              <span v-if="movie.title.length > 25">...</span>
            </p>
            <p class="release">
              Released:
              {{
                new Date(movie.release_date).toLocaleString('en-us', {
                  month: 'long',
                  day: 'numeric',
                  year: 'numeric',
                })
              }}
            </p>
            <NuxtLink
              id="button"
              :to="{ name: 'movies-id', params: { id: movie.id } }"
            >Get More Info</NuxtLink>
          </div>
        </div>
      </div>

      <!-- Now Streaming  -->
      <div id="movie-grid" v-else class="movies-grid">
        <div class="movie" v-for="(movie, index) in movies" :key="index">
          <div class="movie-img">
            <img :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`" alt />
            <p class="review">{{ movie.vote_average }}</p>
            <p class="overview">{{ movie.overview }}</p>
          </div>
          <div class="info">
            <!-- <p class="title">
              {{
                movie.title.slice(0, 25)
              }}
              <span v-if="movie.title.length > 25">...</span>
            </p> -->
            <p class="release">
              Released:
              {{
                new Date(movie.release_date).toLocaleString('en-us', {
                  day: 'numeric',
                  month: 'long',                  
                  year: 'numeric',
                })
              }}
            </p>
            <NuxtLink
              id="button"
              :to="{ name: 'movies-movieid', params: { id: movie.id } }"
            >Get More Info</NuxtLink>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'home-page',
  data() {
    return {
      movies: [],
      searchedMovies: [],
      searchInput: '',
    }
  },
  async fetch() {
    if (this.searchInput === '') {
      await this.getMovies()
      return
    }
    if (this.searchInput !== '') {
      await this.searchMovies()
    }
  },
  fetchDelay: 1000,
  methods: {
    async getMovies() {
      const data = axios.get(
        `https://api.themoviedb.org/3/movie/now_playing?api_key=d84daf9ffd56d83fb8b21434d969c1f6&language=en-US&page=1`
      )
      const result = await data
      result.data.results.forEach((movie) => {
        this.movies.push(movie)
      })
    },
    async searchMovies() {
      const data = axios.get(
        `https://api.themoviedb.org/3/search/movie?api_key=d84daf9ffd56d83fb8b21434d969c1f6&language=en-US&page=1&query=${this.searchInput}`
      )
      const result = await data
      result.data.results.forEach((movie) => {
        this.searchedMovies.push(movie)
      })
    },
    clearSearch() {
      this.searchInput = ''
      this.searchedMovies = []
    },
  },
  watch: {
    searchInput() {
      console.log(this.searchInput)
    },
  },
}
</script>

<style lang="scss">
@import "./assets/variables.scss";

#button {
  display: inline-block;
  text-decoration: none;
  color: #efefef;
  padding: 0.5rem;
  background-color: $primary-color;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: 0.3s ease all;
  align-items: center;
  font-family: $font-family;
  &:hover {
    color: #010101;
    background-color: transparent;
  }
}
.home {
  .loading {
    padding-top: 120px;
    align-items: flex-start;
  }
  .search {
    display: flex;
    padding: 32px 16px;
    align-items: center;
    input {
      max-width: 350px;
      width: 50%;
      padding: 12px 6px;
      font-size: 14px;
      border: none;
      &:focus {
        outline: none;
      }
    }
    .button {
      border-top-left-radius: 0;
      border-bottom-left-radius: 0
    }
  }
  .movies {
    padding: 32px 16px;
    .movies-grid {
      display: grid;
      column-gap: 32px;
      row-gap: 64px;
      grid-template-columns: 1fr;
      @media (min-width: 500px) {
        grid-template-columns: repeat(2, 1fr);
      }
      @media (min-width: 750px) {
        grid-template-columns: repeat(2, 1fr);
      }
      @media (min-width: 1100px) {
        grid-template-columns: repeat(4, 1fr);
      }
      .movie {
        position: relative;
        display: flex;
        flex-direction: column;
        .movie-img {
          position: relative;
          overflow: hidden;
          &:hover {
            .overview {
              transform: translateY(0);
            }
          }
          img {
            display: block;
            width: 100%;
            height: 100%;
          }
          .review {
            position: absolute;
            top: 0;
            left: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            width: 40px;
            height: 40px;
            background-color: #c92502;
            color: #fff;
            border-radius: 0 0 16px 0;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1),
              0 2px 4px -1px rgba(0, 0, 0, 0.06);
          }
          .overview {
            font-family: $font-family;
            line-height: 1.5;
            position: absolute;
            bottom: 0;
            background-color: rgba(201, 38, 2, 0.9);
            padding: 12px;
            color: #fff;
            transform: translateY(100%);
            transition: 0.3s ease-in-out all;
          }
        }
        .info {
          margin-top: auto;
          .title {
            margin-top: 8px;
            color: #fff;
            font-size: 20px;
          }
          .release {
            margin-top: 1rem;
            color: $primary-color;
            font-family: $font-family;
          }
          #button {
            margin-top: 8px;
            font-family: $font-family;
            &:hover {
              color: #010101;
              border-color: transparent;
            }
          }
        }
      }
    }
  }
}
</style>