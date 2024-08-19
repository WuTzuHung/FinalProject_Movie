<template>
  <div class="view">
    <div class="wrapper">
      <button type="button" value="1" :class="{ active: selectedTab === '正在熱映' }" @click="selectTab('正在熱映')">
        正在熱映
      </button>
      <button type="button" value="2" :class="{ active: selectedTab === '即將上映' }" @click="selectTab('即將上映'); upComing()">
        即將上映
      </button>
    </div>
    <div class="underline">
      <div class="bar" v-if="selectedTab === '正在熱映'"></div>
      <div class="bar1" v-if="selectedTab === '即將上映'"></div>
    </div>



    <div v-if="isLoading"><img style="width:60%; height: 92dvh;" src="./ticket/picture/GIF.gif" alt=""></div>
    <div v-else style="width: 100%;">
      <div class="box-wrapper">
        <div class="post-box" v-for="(movie, index) in paginatedMovies" :key="index" v-if="selectedTab === '正在熱映'">
          <div class="post"><img :src="'https://image.tmdb.org/t/p/w500' + movie.poster_path" alt=""
              @click="gotointroduce(movie)">
              <div >
            <div style="color:black;" class="title">{{ movie.title }}</div>
            <!-- <div class="title1">{{ movie.original_title }}</div>
            <div>評分:{{ movie.vote_average === 0 ? '尚未有評分' : movie.vote_average }}</div> -->
            <div style="color:black; margin-top: 0px; margin-bottom: 8px;">上映日:{{ movie.release_date }}</div>
          </div>
          </div>
        </div>
      </div>

      <div class="box-wrapper">
        <div class="post-box" v-for="(movie, index) in paginatedUpcomingMovies" :key="index"
          v-if="selectedTab === '即將上映'">
          <div class="post"><img :src="'https://image.tmdb.org/t/p/w500' + movie.poster_path" alt=""
              @click="gotointroduce(movie)">
              <div style="">
            <div class="title" style="color:black; margin-top: 17px;">{{ movie.title }}</div>
            <!-- <div class="title1">{{ movie.original_title }}</div>
            <div>評分:{{ movie.vote_average === 0 ? '尚未有評分' : movie.vote_average }}</div> -->
            <div style="color:black; margin-top: 12px; margin-bottom: 8px;">上映日:{{ movie.release_date }}</div>
          </div>
          </div>
        </div>
      </div>

      <div class="pagination" v-if="selectedTab === '正在熱映'">
        <button @click="changePage('prev')" :disabled="currentPage === 1">上一頁</button>
        <button v-for="number in pageNumbers" :key="number" @click="goToPage(number)"
          :class="{ 'active-page': number === currentPage }">{{ number }}</button>
        <button @click="changePage('next')" :disabled="currentPage * itemsPerPage >= objPlayingMovie.length">下一頁</button>
      </div>

      <div class="pagination" v-if="selectedTab === '即將上映'">
        <button @click="changeUpcomingPage('prev')" :disabled="upcomingCurrentPage === 1">上一頁</button>
        <button v-for="number in upcomingPageNumbers" :key="number" @click="goToUpcomingPage(number)"
        :class="{ 'active-page': number === upcomingCurrentPage }">{{ number }}</button>
        <button @click="changeUpcomingPage('next')"
          :disabled="upcomingCurrentPage * upcomingItemsPerPage >= objUpComing.length">下一頁</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selectedTab: "正在熱映",
      objPlayingMovie: [],
      objUpComing: [],
      //熱映中分頁數量跟所在頁碼
      itemsPerPage: 1,
      currentPage: 1,
      //即將上映中分頁數量跟所在頁碼
      upcomingItemsPerPage: 1,
      upcomingCurrentPage: 1,
      //過場
      isLoading: false,
    };
  },
  methods: {
    //回到最上方
    scrollToTop() {
      window.scrollTo(0, 0);
    },
    updateItemsPerSlide() {
      const screenWidth = window.innerWidth;
      if (screenWidth <= 767) {
        this.itemsPerPage = 5; // 手机尺寸时每页显示1个项目
        this.upcomingItemsPerPage = 5;
      } else {
        this.itemsPerPage = 12; // 桌面尺寸时每页显示3个项目
        this.upcomingItemsPerPage = 12;
      }
    },
    //即將上映的頁碼轉跳
    goToUpcomingPage(page) {
      this.upcomingCurrentPage = page;
      this.scrollToTop()
    },
    //正在熱映的頁碼轉跳
    goToPage(page) {
      this.currentPage = page;
      this.scrollToTop()
    },
    //正在熱映的換頁方式
    changePage(action) {
      if (action === 'prev' && this.currentPage > 1) {
        this.currentPage--;
      } else if (action === 'next' && (this.currentPage * this.itemsPerPage) < this.objPlayingMovie.length) {
        this.currentPage++;
      }
      this.scrollToTop()
    },
    //即將上映的換頁方式
    changeUpcomingPage(action) {
      if (action === 'prev' && this.upcomingCurrentPage > 1) {
        this.upcomingCurrentPage--;
      } else if (action === 'next' && (this.upcomingCurrentPage * this.upcomingItemsPerPage) < this.objUpComing.length) {
        this.upcomingCurrentPage++;
      }
      this.scrollToTop()
    },
    //正在熱映跟即將上映的切換方式
    selectTab(tab) {
      this.selectedTab = tab;
      this.currentPage = 1
      this.upcomingCurrentPage = 1
    },
    //轉跳到電影介紹的方法
    gotointroduce(movie) {
      console.log(movie)
      this.$router.push({
        name: 'moviecomment',
        query: {
          movieGenreid: movie.genre_ids,
          movieId: movie.id,
          movieOriginaltitle: movie.original_title,
          movieTitle: movie.title,
          movieOverview: movie.overview,
          moviePoster: movie.poster_path,
          movieBack: movie.backdrop_path,
          movieReleasedate: movie.release_date,
          movieVoteavg: movie.vote_average,
        }
      });
    },
    //上映中抓取
    async nowPlaying() {
      this.isLoading = true;
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
            if (!playingMovies.some((existingMovie) => existingMovie.title === movie.title)) {
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
        const playMovies = playingMovies.filter((movie) => movie.poster_path && movie.overview).slice(0, count).sort((a, b) => new Date(a.release_date) - new Date(b.release_date));
        this.objPlayingMovie = playMovies
        console.log('上映中 PlayMovies:', this.objPlayingMovie);
      } catch (error) {
        console.error(error);
      } finally {
        this.isLoading = false; // 无论加载成功或失败，都将 isLoading 设置为 false
      }
    },//即將上映抓取
    async upComing() {
      this.isLoading = true;
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
            if (!comingMovies.some((existingMovie) => existingMovie.title === movie.title)) {
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
        const comeMovies = comingMovies.filter((movie) => movie.poster_path).slice(0, count).sort((a, b) => new Date(a.release_date) - new Date(b.release_date));
        this.objUpComing = comeMovies;
        console.log('即將上映 ComeMovies:', this.objUpComing);
      } catch (error) {
        console.error(error);
      } finally {
        this.isLoading = false; // 无论加载成功或失败，都将 isLoading 设置为 false
      }
    },
  },
  mounted() {
    this.nowPlaying()
    // 先设置itemsPerSlide以避免在加载数据前出现不合适的显示
  this.updateItemsPerSlide();
  this.updateItemsPerSlide();
    window.addEventListener('resize', this.updateItemsPerSlide);
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.updateItemsPerSlide);
  },
  computed: {
    //把正在熱映的資料數切成index
    paginatedMovies() {
      const startIndex = (this.currentPage - 1) * this.itemsPerPage;
      const endIndex = startIndex + this.itemsPerPage;
      return this.objPlayingMovie.slice(startIndex, endIndex);
    },
    //把即將上映的資料切成index
    paginatedUpcomingMovies() {
      const startIndex = (this.upcomingCurrentPage - 1) * this.upcomingItemsPerPage;
      const endIndex = startIndex + this.upcomingItemsPerPage;
      return this.objUpComing.slice(startIndex, endIndex);
    },
    //正在熱映的總頁數
    totalPages() {
      return Math.ceil(this.objPlayingMovie.length / this.itemsPerPage);
    },
    //正在熱映的頁碼
    pageNumbers() {
      const numbers = [];
      for (let i = 1; i <= this.totalPages; i++) {
        numbers.push(i);
      }
      return numbers;
    },
    //即將上映的總頁數
    upcomingTotalPages() {
      return Math.ceil(this.objUpComing.length / this.upcomingItemsPerPage);
    },
    //即將上映的頁碼
    upcomingPageNumbers() {
      const numbers = [];
      for (let i = 1; i <= this.upcomingTotalPages; i++) {
        numbers.push(i);
      }
      return numbers;
    },
  }
};
</script>

<style scoped lang="scss">
.view {
  width: 100%;
  // height: 270vh;
  background-color: white;

  .wrapper {
    width: 100%;
    display: flex;
    justify-content: center;
    font-size: 1.5em;
    line-height: 7dvh;

    button {
      width: 12%;
      margin-top: 5dvh;
      margin-right: 0.3%;
      border: none;
      background-color: #a4b3cc;

      cursor: pointer;

      &.active {
        color: white; // 正在熱映時的顏色
      }

      &:not(.active) {
        color: rgb(202, 202, 202); // 即將上映時的顏色
      }
    }
  }

  .underline {
    width: 17%;
    text-align: center;
    margin: 1dvh auto 8dvh;
    height: 2px;
    margin-left: 41.42%;
    border-bottom: 1px solid rgba(0, 0, 0, 0.3);

    .bar,
    .bar1 {
      width: 50%;
      height: 0.1dvh;
      margin-bottom: -2dvh;
      background-color: rgb(245, 120, 141);
    }

    .bar {
      transform: translate(0%);
    }

    .bar1 {
      transform: translate(100%);
    }
  }

  .box-wrapper {
    width: 98%;
    display: flex;
    color: white;
    flex-wrap: wrap;
    margin: 0;
    text-align: center;
    justify-content: center;
    margin-left: 2%;
    // border: 1px solid black;

    .post-box {
      width: 300px;
      height: 80vh;
      margin-right: 35px;
    // border: 1px solid black;
    // border: 1px solid black;


      .post {
        width: 300px;
        height: 60vh;
        max-height: 60vh;
        

        img {
          height: 100%;
          width: 100%;
          margin-right: 10px;
          border-radius: 10px 10px 0 0;
          object-fit: cover; // 保持圖片比例並填滿 img 區域
          cursor: pointer;
          
        }

        .title {
          font-size: 20pt;
          width: 270px;
          margin-left: 17px;
          margin-top: 10px;
          
          // border: 1px solid black;
          text-align: center;
          text-overflow: ellipsis;
          white-space: nowrap;
          overflow: hidden;
        }

        .title1 {
          width: 270px;
          margin-left: 17px;
          color: rgb(158, 158, 158);
          text-align: center;
          text-overflow: ellipsis;
          white-space: nowrap;
          overflow: hidden;
        }

      }
    }
  }
}

.pagination {
  width: 100%;
  justify-content: center;
  align-items: center;

  button {
    color: rgb(158, 158, 158);
    font-size: 18pt;
    background-color: white;
    border: 0px;
    margin-bottom: 12px;

    &.active-page,
    &:hover {
      color: salmon; // 上一页、下一页的颜色
    }
  }

}

.pagination {
        width: 100%;
        justify-content: center;
        align-items: center;
        button {
            color: rgb(0, 0, 0);
            font-size: 18pt;
            // background-color: #525f75;
            border: 0px;
            

            &.active-page,
            &:hover {
                color: rgb(141, 141, 125); // 上一页、下一页的颜色
            }
        }

    }

@media (max-width: 767px){

  .view {
  

  .wrapper {
    font-size: 1em;
    

    button {
      width: 30%;
      

      cursor: pointer;

      &.active {
        
      }

      &:not(.active) {
        
      }
    }
  }

  .underline {
    width: 30%;
    margin-left: 35%;

    .bar,
    .bar1 {
      
    }

    .bar {
      
    }

    .bar1 {
      
    }
  }

  .box-wrapper {
    width: 92%;
    margin-left: 8%;
    

    .post-box {
      


      .post {
        
        

        img {
          
          
        }

        .title {
          
        }

        .title1 {
          
        }

      }
    }
  }
}

.pagination {
  

  button {
    

    &.active-page,
    &:hover {
      
    }
  }

}

.pagination {
        
        button {
            

            &.active-page,
            &:hover {
                
            }
        }

    }

}
</style>  