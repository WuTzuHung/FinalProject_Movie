<script>
export default {
  data() {
    return {
      objPlayMovies: [], // 熱映中電影
      objComeMovies: [], // 即將上映電影
      objPopularMovies: [], // 歷史熱門電影
      objtype: [], // 電影所有類型
      objSearchMovies: [], // 搜尋電影
      objTypeMovies: [], // 選擇類型找電影
      itemsPerSlide: 1, // 每頁顯示的輪播項目數量
      currentSlide: 0,
      selectedType: "", // 選單選到的類型
      searchText: "", // 搜尋電影
    };
  },
  computed: {
    playPerPage() {
      let cutArray = [];
      for (let i = 0; i < this.objPlayMovies.length; i += this.itemsPerSlide) {
        cutArray.push(this.objPlayMovies.slice(i, i + this.itemsPerSlide));
      }
      return cutArray;
    },
    comePerPage() {
      let cutArray = [];
      for (let i = 0; i < this.objComeMovies.length; i += this.itemsPerSlide) {
        cutArray.push(this.objComeMovies.slice(i, i + this.itemsPerSlide));
      }
      return cutArray;
    },
    popularPerPage() {
      let cutArray = [];
      for (
        let i = 0;
        i < this.objPopularMovies.length;
        i += this.itemsPerSlide
      ) {
        cutArray.push(this.objPopularMovies.slice(i, i + this.itemsPerSlide));
      }
      return cutArray;
    },
    typePerPage() {
      const cutArray = [];
      for (
        let i = 0;
        i < this.objTypeMovies.length;
        i += this.itemsPerSlide
      ) {
        cutArray.push(
          this.objTypeMovies.slice(i, i + this.itemsPerSlide)
        );
      }
      console.log("typePerPage:", cutArray);
      return cutArray;
    },
    searchPerPage() {
      const cutArray = [];
      for (
        let i = 0;
        i < this.objSearchMovies.length;
        i += this.itemsPerSlide
      ) {
        cutArray.push(this.objSearchMovies.slice(i, i + this.itemsPerSlide));
      }
      console.log("searchPerPage:", cutArray);
      return cutArray;
    },
  },
  methods: {
    async getPlayMovie() { // 上映中
      const options = {
        method: "GET",
        headers: {
          accept: "application/json",
          Authorization:
            "Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiIxZTBiNGVhYWYyMjVhZTdmYzFhNjdjYzk0ODk5Mjk5OSIsInN1YiI6IjY1N2ZjYzAzMGU2NGFmMDgxZWE4Mjc3YSIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.3d6GcXTBf2kwGx9GzG7O4_8eCoHAjGxXNr9vV1lVXww",
        },
      };
      let page = 1;
      let count = 30; //要抓的電影數
      let playingMovies = [];

      try {
        const nowDate = new Date();
        const twoMonth = new Date();
        twoMonth.setMonth(nowDate.getMonth() - 2);

        while (playingMovies.length < count) {
          const api = `https://api.themoviedb.org/3/movie/now_playing?language=zh-TW&page=${page}`;
          const response = await fetch(api, options);

          if (!response.ok) {
            throw new Error("Network response was not ok");
          }
          const data = await response.json();
          const moviesOnPage = data.results.filter((movie) => {
            const releaseDate = new Date(movie.release_date);
            // 檢查發佈日期是否在指定範圍內
            if (!(releaseDate >= twoMonth && releaseDate <= nowDate)) {
              return false;
            }
            // 檢查poster_path是否存在
            if (!movie.poster_path) {
              return false;
            }
            return true;
          });
          // 移除已存在的電影，避免重複
          for (const movie of moviesOnPage) {
            if (
              !playingMovies.some(
                (existingMovie) => existingMovie.title === movie.title
              )
            ) {
              playingMovies.push(movie);
            }
          }
          if (page < data.total_pages) {
            page++;
          } else {
            break;
          }
        }
        // 過濾掉沒有 poster_path 的電影
        const playMovies = playingMovies
          .filter((movie) => movie.poster_path)
          .slice(0, count)
          .sort((a, b) => new Date(a.release_date) - new Date(b.release_date));
        this.objPlayMovies = playMovies;
        console.log("上映中 PlayMovies:", this.objPlayMovies);
      } catch (error) {
        console.error(error);
      }
    },
    updateItemsPerSlide() {
      const screenWidth = window.innerWidth;
      if (screenWidth <= 767) {
        this.itemsPerSlide = 1; // 手机尺寸时每页显示1个项目
      } else {
        this.itemsPerSlide = 3; // 桌面尺寸时每页显示3个项目
      }
    },
    async getComeMovie() { // 即將上映
      const options = {
        method: "GET",
        headers: {
          accept: "application/json",
          Authorization:
            "Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiIxZTBiNGVhYWYyMjVhZTdmYzFhNjdjYzk0ODk5Mjk5OSIsInN1YiI6IjY1N2ZjYzAzMGU2NGFmMDgxZWE4Mjc3YSIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.3d6GcXTBf2kwGx9GzG7O4_8eCoHAjGxXNr9vV1lVXww",
        },
      };

      let page = 1;
      let count = 30; //要抓的電影數
      let comingMovies = [];

      try {
        const nowDate = new Date();
        const oneMonth = new Date();
        oneMonth.setMonth(nowDate.getMonth() + 1);

        while (comingMovies.length < count) {
          const api = `https://api.themoviedb.org/3/movie/upcoming?language=zh-TW&page=${page}`;
          const response = await fetch(api, options);

          if (!response.ok) {
            throw new Error("Network response was not ok");
          }
          const data = await response.json();
          const moviesOnPage = data.results.filter((movie) => {
            const releaseDate = new Date(movie.release_date);
            // 檢查發佈日期是否在指定範圍內
            if (!(releaseDate >= nowDate && releaseDate <= oneMonth)) {
              return false;
            }
            // 檢查poster_path是否存在
            if (!movie.poster_path) {
              return false;
            }
            return true;
          });
          // 移除已存在的電影，避免重複
          for (const movie of moviesOnPage) {
            if (
              !comingMovies.some(
                (existingMovie) => existingMovie.title === movie.title
              )
            ) {
              comingMovies.push(movie);
            }
          }
          if (page < data.total_pages) {
            page++;
          } else {
            break;
          }
        }
        // 截取前 count 筆資料
        const comeMovies = comingMovies
          .filter((movie) => movie.poster_path)
          .slice(0, count)
          .sort((a, b) => new Date(a.release_date) - new Date(b.release_date));
        this.objComeMovies = comeMovies;
        console.log("即將上映 ComeMovies:", this.objComeMovies);
      } catch (error) {
        console.error(error);
      }
    },
    async getPopularMovie() { // 歷史熱門電影
      const options = {
        method: "GET",
        headers: {
          accept: "application/json",
          Authorization:
            "Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiIxZTBiNGVhYWYyMjVhZTdmYzFhNjdjYzk0ODk5Mjk5OSIsInN1YiI6IjY1N2ZjYzAzMGU2NGFmMDgxZWE4Mjc3YSIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.3d6GcXTBf2kwGx9GzG7O4_8eCoHAjGxXNr9vV1lVXww",
        },
      };

      let page = 1;
      let count = 50; //要抓的電影數
      let popularMovies = [];

      try {
        while (popularMovies.length < count) {
          const api = `https://api.themoviedb.org/3/discover/movie?include_adult=false&include_video=false&language=zh-TW&&page=${page}&sort_by=revenue.desc`;
          const response = await fetch(api, options);
          if (!response.ok) {
            throw new Error("Network response was not ok");
          }
          const data = await response.json();
          const moviesOnPage = data.results.filter((movie) => {
            // 檢查poster_path是否存在
            if (!movie.poster_path) {
              return false;
            }
            return true;
          });
          // 移除已存在的電影，避免重複
          for (const movie of moviesOnPage) {
            if (
              !popularMovies.some(
                (existingMovie) => existingMovie.title === movie.title
              )
            ) {
              popularMovies.push(movie);
            }
          }
          if (page < data.total_pages) {
            page++;
          } else {
            break;
          }
        }
        // 截取前 count 筆資料
        const popularMovie = popularMovies
          .filter((movie) => movie.poster_path)
          .slice(0, count)
          .sort((a, b) => b.vote_average - a.vote_average);
        this.objPopularMovies = popularMovie;
        console.log("最受歡迎 popularMovies:", this.objPopularMovies);
      } catch (error) {
        console.error(error);
      }
    },
    async searchMovie() { // 搜尋電影
      const options = {
        method: "GET",
        headers: {
          accept: "application/json",
          Authorization:
            "Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiIxZTBiNGVhYWYyMjVhZTdmYzFhNjdjYzk0ODk5Mjk5OSIsInN1YiI6IjY1N2ZjYzAzMGU2NGFmMDgxZWE4Mjc3YSIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.3d6GcXTBf2kwGx9GzG7O4_8eCoHAjGxXNr9vV1lVXww",
        },
      };

      let page = 1;
      let count = 30; //要抓的電影數
      let searchMovies = [];

      try {
        while (searchMovies.length < count) {
          const api = `https://api.themoviedb.org/3/search/movie?query=${this.searchText}&include_adult=false&language=zh-tw&page=${page}`;
          const response = await fetch(api, options);

          if (!response.ok) {
            throw new Error("Network response was not ok");
          }
          const data = await response.json();
          const moviesOnPage = data.results.filter((movie) => {
            // 檢查poster_path是否存在
            if (!movie.poster_path) {
              return false;
            }
            return true;
          });
          // 移除已存在的電影，避免重複
          for (const movie of moviesOnPage) {
            if (
              !searchMovies.some(
                (existingMovie) => existingMovie.title === movie.title
              )
            ) {
              searchMovies.push(movie);
            }
          }
          if (page < data.total_pages) {
            page++;
          } else {
            break;
          }
        }
        const searchOfMovies = searchMovies
          .filter((movie) => movie.poster_path)
          .slice(0, count)
          .sort((a, b) => b.vote_average - a.vote_average);
        this.objSearchMovies = searchOfMovies;
        console.log("搜尋結果", this.objSearchMovies);
      } catch (error) {
        console.error(error);
      }
    },
    async getTypeMovie(event) { // 選單找電影
      const options = {
        method: "GET",
        headers: {
          accept: "application/json",
          Authorization:
            "Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiIxZTBiNGVhYWYyMjVhZTdmYzFhNjdjYzk0ODk5Mjk5OSIsInN1YiI6IjY1N2ZjYzAzMGU2NGFmMDgxZWE4Mjc3YSIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.3d6GcXTBf2kwGx9GzG7O4_8eCoHAjGxXNr9vV1lVXww",
        },
      };
      // 取得選單值
      this.selectedType = event.target.value;
      console.log(this.selectedType);
      let genres = "";
      for (let i = 0; i < this.objtype.length; i++) {
        if (this.selectedType === this.objtype[i].name) {
          genres = this.type[i].id;
        }
      }
      console.log(genres);
      let page = 1;
      let count = 90; //要抓的電影數
      let typeOfMovies = [];

      try {
        while (typeOfMovies.length < count) {
          const api = `https://api.themoviedb.org/3/discover/movie?include_adult=false&include_video=false&language=en-US&page=1&sort_by=popularity.desc&with_genres=${genres}`;
          const response = await fetch(api, options);
          if (!response.ok) {
            throw new Error("Network response was not ok");
          }
          const data = await response.json();
          const moviesOnPage = data.results.filter((movie) => {
            // 檢查poster_path是否存在
            if (!movie.poster_path) {
              return false;
            }
            return true;
          });
          // 移除已存在的電影，避免重複
          for (const movie of moviesOnPage) {
            if (
              !typeOfMovies.some(
                (typeOfMovies) => typeOfMovies.title === movie.title
              )
            ) {
              typeOfMovies.push(movie);
            }
          }
          if (page < data.total_pages) {
            page++;
          } else {
            break;
          }
        }
        // 截取前 count 筆資料
        const typeOfSearchMovies = typeOfMovies.filter((movie) => movie.poster_path).slice(0, count).sort((a, b) => new Date(a.release_date) - new Date(b.release_date));
        this.objTypeMovies = typeOfSearchMovies;
        console.log("選擇類型電影:", this.objTypeMovies);
        // this.splitMovies();
      } catch (error) {
        console.error(error);
      }
    },
    getPlayPerson(movieId) { //上映中 演員*5 + 導演*1
      const options = {
        method: "GET",
        headers: {
          accept: "application/json",
          Authorization:
            "Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiIxZTBiNGVhYWYyMjVhZTdmYzFhNjdjYzk0ODk5Mjk5OSIsInN1YiI6IjY1N2ZjYzAzMGU2NGFmMDgxZWE4Mjc3YSIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.3d6GcXTBf2kwGx9GzG7O4_8eCoHAjGxXNr9vV1lVXww",
        },
      };

      // fetch(`https://api.themoviedb.org/3/movie/1029575/credits?language=en-US`, options)
      fetch(
        `https://api.themoviedb.org/3/movie/${movieId}/credits?language=en-US`,
        options
      )
        .then((response) => response.json())
        .then((response) => {
          const directors = response.crew.filter(
            (person) => person.job === "Director"
          );
          const cast = response.cast.slice(0, 5);
          console.log(directors);
          console.log(cast);

          let playPeople = [];
          playPeople.push(directors);
          playPeople.push(cast);
          console.log("全部電影的上映中 演員 objPlayPerson", this.playPeople);
        })
        .catch((error) => {
          console.error(error);
        });
    },
    getComePerson(movieId) { //即將上映 演員*5 + 導演*1
      const options = {
        method: "GET",
        headers: {
          accept: "application/json",
          Authorization:
            "Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiIxZTBiNGVhYWYyMjVhZTdmYzFhNjdjYzk0ODk5Mjk5OSIsInN1YiI6IjY1N2ZjYzAzMGU2NGFmMDgxZWE4Mjc3YSIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.3d6GcXTBf2kwGx9GzG7O4_8eCoHAjGxXNr9vV1lVXww",
        },
      };

      // fetch(`https://api.themoviedb.org/3/movie/1029575/credits?language=en-US`, options)
      fetch(
        `https://api.themoviedb.org/3/movie/${movieId}/credits?language=en-US`,
        options
      )
        .then((response) => response.json())
        .then((response) => {
          const directors = response.crew.filter(
            (person) => person.job === "Director"
          );
          const cast = response.cast.slice(0, 5);
          console.log(directors);
          console.log(cast);

          let comePeople = [];
          comePeople.push(directors);
          comePeople.push(cast);
          this.objComePerson.push(comePeople);
          // console.log('最後的人員 comePeople', comePeople);
          console.log(
            "全部電影的即將上映 演員 objComePerson",
            this.objComePerson
          );
        })
        .catch((error) => {
          console.error(error);
        });
    },
    getPlayTrailer(movieId) { //上映中 預告片
      const options = {
        method: "GET",
        headers: {
          accept: "application/json",
          Authorization:
            "Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiIxZTBiNGVhYWYyMjVhZTdmYzFhNjdjYzk0ODk5Mjk5OSIsInN1YiI6IjY1N2ZjYzAzMGU2NGFmMDgxZWE4Mjc3YSIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.3d6GcXTBf2kwGx9GzG7O4_8eCoHAjGxXNr9vV1lVXww",
        },
      };

      // fetch(`https://api.themoviedb.org/3/movie/1029575/videos?language=en-US`, options)
      fetch(
        `https://api.themoviedb.org/3/movie/${movieId}/videos?language=en-US`,
        options
      )
        .then((response) => {
          const firstTrailerKey = response.results?.[0]?.key;

          const trailerLink = `https://www.youtube.com/watch?v=${firstTrailerKey}`;

          console.log("PlayTrailerLink", trailerLink);
        })
        .catch((error) => {
          console.error(error);
        });
    },
    getMovieType() { //電影類型
      const options = {
        method: "GET",
        headers: {
          accept: "application/json",
          Authorization:
            "Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiIxZTBiNGVhYWYyMjVhZTdmYzFhNjdjYzk0ODk5Mjk5OSIsInN1YiI6IjY1N2ZjYzAzMGU2NGFmMDgxZWE4Mjc3YSIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.3d6GcXTBf2kwGx9GzG7O4_8eCoHAjGxXNr9vV1lVXww",
        },
      };
      fetch(
        "https://api.themoviedb.org/3/genre/movie/list?language=en",
        options
      )
        .then((response) => response.json())
        .then((response) => {
          (this.type = response.genres), console.log(this.type);
          this.objtype = this.type;
        })
        .catch((err) => console.error(err));
    },
    chooseMovie(item) { // 點電影飛去新路由
      console.log(item);
      this.$router.push({
        name: "moviecomment",
        query: {
          movieGenreid: item.genre_ids,
          movieId: item.id,
          movieOriginaltitle: item.original_title,
          movieTitle: item.title,
          movieOverview: item.overview,
          moviePoster: item.poster_path,
          movieBack: item.backdrop_path,
          movieReleasedate: item.release_date,
          movieVoteavg: item.vote_average,
        },
      });
    },
    nextSlide() {
      this.currentSlide = (this.currentSlide + 1) % this.itemscutArray.length;
    },
    prevSlide() {
      this.currentSlide =
        (this.currentSlide - 1 + this.itemscutArray.length) %
        this.itemscutArray.length;
    },
  },
  async mounted() {
  // 先设置itemsPerSlide以避免在加载数据前出现不合适的显示
  this.updateItemsPerSlide();
    await this.getPlayMovie();
    await this.getComeMovie();
    await this.getPopularMovie();
    this.getMovieType();
    this.updateItemsPerSlide();
    window.addEventListener('resize', this.updateItemsPerSlide);
    },
    beforeDestroy() {
    window.removeEventListener('resize', this.updateItemsPerSlide);
  },
};
</script>

<template>
  <h1>上映中電影</h1>
  <div class="container">
    <div id="carouselExample" class="carousel slide" data-bs-ride="carousel" data-bs-interval="3000">
      <div class="carousel-inner">
        <div v-for="(itemsChunk, index) in playPerPage" :key="index" :class="['carousel-item', index === currentSlide ? 'active' : '']">
          <div class="row" >
            <div v-for="(item, innerIndex) in itemsChunk" :key="innerIndex" class="col-md-4">
              <a class="aitem" @click="chooseMovie(item)">
                <div class="card">
                  <img :src="'https://image.tmdb.org/t/p/w500' + item.poster_path" class="d-block w-100 card-img-top" alt="無電影海報"/>
                  <div class="card-body">
                      <p class="card-text">
                        <span>{{ item.title }}</span><br />
                        <span>{{ "上映日期：" + item.release_date }}</span>
                      </p>
                  </div>
                </div>
              </a>
              <div class="carousel-caption d-none d-md-block"></div>
            </div>
          </div>
        </div>
      </div>
      <button class="carousel-control-prev" type="button" data-bs-target="#carouselExample" data-bs-slide="prev" @click="prevSlide" >
        <span class="carousel-control-prev-icon" aria-hidden="true">
          <i class="fa-solid fa-circle-arrow-left" ></i>
        </span>
        <span class="visually-hidden">Previous</span>
      </button>
      <button class="carousel-control-next" type="button" data-bs-target="#carouselExample" data-bs-slide="next" @click="nextSlide" >
        <span class="carousel-control-next-icon" aria-hidden="true">
          <i class="fa-solid fa-circle-arrow-right" >
            </i></span>
        <span class="visually-hidden">Next</span>
      </button>
    </div>
  </div>
  <h1>近期上映電影</h1>
  <div class="container">
    <div id="carouselExample1" class="carousel slide" data-bs-ride="carousel" data-bs-interval="3000">
      <div class="carousel-inner">
        <div v-for="(itemsChunk, index) in comePerPage" :key="index" :class="['carousel-item', index === currentSlide ? 'active' : '']">
          <div class="row">
            <div v-for="(item, innerIndex) in itemsChunk" :key="innerIndex" class="col-md-4">
              <a class="aitem" @click="chooseMovie(item)">
                <div class="card">
                  <img :src="'https://image.tmdb.org/t/p/w500' + item.poster_path" class="d-block w-100 card-img-top" alt="無電影海報"/>
                  <div class="card-body">
                      <p class="card-text">
                        <span>{{ item.title }}</span><br />
                        <span>{{ "上映日期：" + item.release_date }}</span>
                      </p>
                  </div>
                </div>
              </a>
              <div class="carousel-caption d-none d-md-block"></div>
            </div>
          </div>
        </div>
      </div>
      <button class="carousel-control-prev" type="button" data-bs-target="#carouselExample1" data-bs-slide="prev" @click="prevSlide" >
        <span class="carousel-control-prev-icon" aria-hidden="true">
          <i class="fa-solid fa-circle-arrow-left"></i>
        </span>
        <span class="visually-hidden">Previous</span>
      </button>
      <button class="carousel-control-next" type="button" data-bs-target="#carouselExample1" data-bs-slide="next" @click="nextSlide" >
        <span class="carousel-control-next-icon" aria-hidden="true">
          <i class="fa-solid fa-circle-arrow-right"></i>
        </span>
        <span class="visually-hidden">Next</span>
      </button>
    </div>
  </div>
  <h1>為你推薦</h1>
  <div class="container">
    <div id="carouselExample2" class="carousel slide" data-bs-ride="carousel" data-bs-interval="3000">
      <div class="carousel-inner">
        <div v-for="(itemsChunk, index) in popularPerPage" :key="index" :class="['carousel-item', index === currentSlide ? 'active' : '']">
          <div class="row">
            <div v-for="(item, innerIndex) in itemsChunk" :key="innerIndex" class="col-md-4">
              <a class="aitem" @click="chooseMovie(item)">
                <div class="card">
                  <img :src="'https://image.tmdb.org/t/p/w500' + item.poster_path" class="d-block w-100 card-img-top" alt="無電影海報"/>
                  <div class="card-body">
                      <p class="card-text">
                        <span>{{ item.title }}</span><br />
                        <span>{{ "上映日期：" + item.release_date }}</span>
                      </p>
                  </div>
                </div>
              </a>
              <div class="carousel-caption d-none d-md-block"></div>
            </div>
          </div>
        </div>
      </div>
      <button class="carousel-control-prev" type="button" data-bs-target="#carouselExample2" data-bs-slide="prev" @click="prevSlide">
        <span class="carousel-control-prev-icon" aria-hidden="true">
          <i class="fa-solid fa-circle-arrow-left"></i>
        </span>
        <span class="visually-hidden">Previous</span>
      </button>
      <button class="carousel-control-next" type="button" data-bs-target="#carouselExample2" data-bs-slide="next" @click="nextSlide">
        <span class="carousel-control-next-icon" aria-hidden="true">
          <i class="fa-solid fa-circle-arrow-right"></i>
        </span>
        <span class="visually-hidden">Next</span>
      </button>
    </div>
  </div>
  <h1>搜尋電影</h1>
  <div class="container">
    <input class="movieSearch" type="text" v-model="searchText" required />
    <button type="submit" class="btn btn-outline-dark" @click="searchMovie">
      搜尋
    </button>

    <div id="carouselExample3" class="carousel slide" data-bs-ride="carousel" data-bs-interval="3000">
      <div class="carousel-inner">
        <div v-for="(itemsChunk, index) in searchPerPage" :key="index" :class="['carousel-item', index === currentSlide ? 'active' : '']">
          <div class="row">
            <div v-for="(item, innerIndex) in itemsChunk" :key="innerIndex" class="col-md-4">
              <a class="aitem" @click="chooseMovie(item)">
                <div class="card">
                  <img :src="'https://image.tmdb.org/t/p/w500' + item.poster_path" class="d-block w-100 card-img-top" alt="無電影海報"/>
                  <div class="card-body">
                      <p class="card-text">
                        <span>{{ item.title }}</span><br />
                        <span>{{ "上映日期：" + item.release_date }}</span>
                      </p>
                  </div>
                </div>
              </a>
              <div class="carousel-caption d-none d-md-block"></div>
            </div>
          </div>
        </div>
      </div>
      <button v-if="searchPerPage.length > 0" class="carousel-control-prev" type="button" data-bs-target="#carouselExample3" data-bs-slide="prev" @click="prevSlide">
        <span class="carousel-control-prev-icon" aria-hidden="true">
          <i class="fa-solid fa-circle-arrow-left"></i>
        </span>
        <span class="visually-hidden">Previous</span>
      </button>
      <button v-if="searchPerPage.length > 0" class="carousel-control-next" type="button" data-bs-target="#carouselExample3" data-bs-slide="next" @click="nextSlide">
        <span class="carousel-control-next-icon" aria-hidden="true">
          <i class="fa-solid fa-circle-arrow-right"></i>
        </span>
        <span class="visually-hidden">Next</span>
      </button>
    </div>
  </div>
  
  <h1>分類選擇</h1>
  <div class="movieType">
    <select @change="getTypeMovie">
      <option v-for="(item, index) in this.objtype" :key="index">
        {{ item.name }}
      </option>
    </select>
  </div>
  <div class="container">
    <div id="customCarousel4" class="carousel slide" data-bs-ride="carousel" data-bs-interval="3000">
      <div class="carousel-inner">
        <div v-for="(itemsChunk, index) in typePerPage" :key="index" :class="['carousel-item', index === currentSlide ? 'active' : '']">
          <div class="row">
            <div v-for="(item, innerIndex) in itemsChunk" :key="innerIndex" class="col-md-4">
              <a class="aitem" @click="chooseMovie(item)">
                <div class="card">
                  <img :src="'https://image.tmdb.org/t/p/w500' + item.poster_path" class="d-block w-100 card-img-top" alt="無電影海報"/>
                  <div class="card-body">
                      <p class="card-text">
                        <span>{{ item.title }}</span><br />
                        <span>{{ "上映日期：" + item.release_date }}</span>
                      </p>
                  </div>
                </div>
              </a>
              <div class="carousel-caption d-none d-md-block"></div>
            </div>
          </div>
        </div>
      </div>
      <button v-if="typePerPage.length > 0" class="carousel-control-prev" type="button" data-bs-target="#customCarousel4" data-bs-slide="prev" @click="prevSlide">
        <span class="carousel-control-prev-icon" aria-hidden="true">
          <i class="fa-solid fa-circle-arrow-left"></i>
        </span>
        <span class="visually-hidden">Previous</span>
      </button>
      <button v-if="typePerPage.length > 0" class="carousel-control-next" type="button" data-bs-target="#customCarousel4" data-bs-slide="next" @click="nextSlide">
        <span class="carousel-control-next-icon" aria-hidden="true">
          <i class="fa-solid fa-circle-arrow-right"></i>
        </span>
        <span class="visually-hidden">Next</span>
      </button>
    </div>
  </div>
</template>

<style scoped lang="scss">

.aitem{
  display: flex;
  justify-content: center;
}

.row{
  display: flex;
  justify-content: center; /* 水平居中 */
}

.col-md-4{
  // max-width: 100%;
}

.container{
  margin: 0%;
  padding: 0px;
  max-width: 100%;
}

a {
  text-decoration: none;
}

h1 {
  margin-top: 2.5dvh;
  text-align: center;
}

p, label, select {
  font-family:'jf-openhuninn-2.0';
  color: rgb(51, 51, 62);
  font-size: 1.1em;
}

span, button {
  width: 5%;
  height: 15dvh;
  font-size: 1.3em;
  color: rgb(51, 51, 62);
}

.carousel-control-prev{
  margin-left: -1%;
  margin-top: 30dvh;
}

.carousel-control-next{
  margin-right: 0.5%;
  margin-top: 30dvh;
}

.card{
  width: 75%;
  margin: 2dvh 2dvh;

  .card-img-top{
    height: 55dvh;
  }

  .card-body{
    min-height: 20dvh;

    .card-text{
      margin-top: 1.3dvh;
    }
  }
}

.movieSearch{
  width: 20%; 
  border-radius: 0%; 
  outline: none; 
  resize: none; 
  border: 0; 
  background: none; 
  border-bottom: 1px solid black;
  margin-bottom: 5dvh;
  margin-top: 5dvh;
  margin-left: 2.5%;
}

.btn-outline-dark{
  padding: 0px 5px;  /* 根据需要调整填充 */
  width: 10%;
  height: 5dvh;
  margin-left: 3%;
}

.movieType {
  button {
    width: 200px;
    height: 100px;
    margin: 30px 0 60px 0;

    &:hover {
      background-color: gray;
    }
  }
}

.textHeader{
  font-family:'jf-openhuninn-2.0';
  font-size: 2em;
  margin: 0;
}

@media (max-width: 767px) {
    .container {
        .carousel {
            .carousel-inner {
              
                .carousel-item {
                  
                    .row {
                      // display: flex;
                      // flex-wrap: nowrap; /* 防止换行 */
                      // overflow: hidden; /* 隐藏溢出的内容 */ 
                        .col-md-4 {
                          // flex: 0 0 100%; /* 每列占据全部宽度 */
                          // max-width: 100%; /* 最大宽度为100% */
                          // padding: 0; /* 去除 padding */
                          .aitem{
                            .card {
                              // width: 70%;
                                .card-img-top {
                                }
                                .card-body {
                                    .card-text {
                                    }
                                }
                            }
                          }
                            .carousel-caption {
                            }
                        }
                    }
                }
            }
            .carousel-control-prev{
              margin-left: 0.5%;
              margin-top: 30dvh;
}

            .carousel-control-next{
              margin-right: 7.5%;
              margin-top: 30dvh;
}
        }
    }
}

// .container {
//         #carouselExample1 {
//             .carousel-inner {
//                 .carousel-item {
//                     .row {
//                         display: flex;
//                         flex-wrap: nowrap; /* 防止换行 */
//                         overflow: hidden; /* 隐藏溢出的内容 */
//                         .col-md-4 {
//                             flex: 0 0 100%; /* 每列占据全部宽度 */
//                             max-width: 100%; /* 最大宽度为100% */
//                             padding: 0; /* 去除 padding */
//                             .aitem {
//                                 .card {
//                                     // width: 100%; /* 可以根据需要设置 */
//                                     .card-img-top {
//                                         width: 100%; /* 确保图片宽度占满 */
//                                     }
//                                     .card-body {
//                                         .card-text {
//                                         }
//                                     }
//                                 }
//                             }
//                             .carousel-caption {
//                             }
//                         }
//                     }
//                 }
//             }
//             .carousel-control-prev,
//             .carousel-control-next {
//                 // 如果需要调整按钮位置，可在此设置
//             }
//         }
//     }
</style>
