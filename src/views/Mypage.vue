<script>
  import { Swiper, SwiperSlide } from 'swiper/vue';
  import 'swiper/css';
  import Cookies from 'js-cookie'
export default {
  data() {
    return {
      //電影相關
      movieInfo: {movieId:891699,
                  movieGenreid:[ "28", "80" ],
                  movieOverview:"平安夜，高德洛克（喬爾金納曼 飾）年幼的兒子因為捲入幫派槍戰而身亡，他自己也受到重傷，經過治療後雖然保住了性命，卻失去了說話的能力，他決定展開一連串嚴格的訓練，誓言要讓害死兒子的幫派份子血債血償……。",
                  movieVoteavg:5.9,
                  movieReleasedate:"2023-11-30",
                  moviePoster:"/r5kvFAqfyDBFZzDY5XTJYxDidsZ.jpg" },
      directors: {},
      casts: {},
      trailerLink: null,
      type: [],
      movieType: [],
      //評論區相關
      mymovie:[
        { title:"0",imgUrl:"/r5kvFAqfyDBFZzDY5XTJYxDidsZ.jpg"},
        { title:"1",imgUrl:"/pHUCVSUCma3LHmb0WUBei1QGUtD.jpg"},
        { title:"2",imgUrl:"/eHBBg8juDeKWj34b5oKru5JkNqt.jpg"},
        { title:"3",imgUrl:"/eHBBg8juDeKWj34b5oKru5JkNqt.jpg"},
        { title:"4",imgUrl:"/r5kvFAqfyDBFZzDY5XTJYxDidsZ.jpg"},
        { title:"5",imgUrl:"/pHUCVSUCma3LHmb0WUBei1QGUtD.jpg"},
        { title:"6",imgUrl:"/eHBBg8juDeKWj34b5oKru5JkNqt.jpg"},
        { title:"7",imgUrl:"/eHBBg8juDeKWj34b5oKru5JkNqt.jpg"},
        { title:"8",imgUrl:"/r5kvFAqfyDBFZzDY5XTJYxDidsZ.jpg"},
        { title:"9",imgUrl:"/pHUCVSUCma3LHmb0WUBei1QGUtD.jpg"},
        { title:"10",imgUrl:"/eHBBg8juDeKWj34b5oKru5JkNqt.jpg"},
        { title:"11",imgUrl:"/eHBBg8juDeKWj34b5oKru5JkNqt.jpg"},
        { title:"12",imgUrl:"/r5kvFAqfyDBFZzDY5XTJYxDidsZ.jpg"},
        { title:"13",imgUrl:"/pHUCVSUCma3LHmb0WUBei1QGUtD.jpg"},
        { title:"14",imgUrl:"/eHBBg8juDeKWj34b5oKru5JkNqt.jpg"},
        { title:"15",imgUrl:"/eHBBg8juDeKWj34b5oKru5JkNqt.jpg"},
      ],
      swiperdata:[],
      swiperdata2:[],
      swiperdata3:[],
      swiperdata4:[],
      name: "John123456",
      commentText: "",
      sortOrder: "sort",
      baoleiButton: false, //暴雷按鈕
      blurredArea: true, //模糊區域
      userLoggedIn:false,
      language:["en-US","zh-TW"],
      languageTarget:"",
      target:"",
      objPlayMovies:{},
      moviecomment:"",
      pages:[],
      searchaccount:"",
      account:""
    };
  },
  computed: {
    scheduleListThree: function () {
    let index = 0;
    let count = 3;
    let arrThree = [];
    let data = this.mymovie;
    for (let i = 0; i < this.mymovie.length; i++) {
      index = parseInt(i / count);
      if (arrThree.length <= index) {
        arrThree.push([]);
    }
    arrThree[index].push(data[i])
    }
    return arrThree
  }
  },
  components: {
      Swiper,
      SwiperSlide,
    },
    setup() {
      const onSwiper = (swiper) => {
        console.log(swiper);
      };
      const onSlideChange = () => {
        console.log('slide change');
      };
      return {
        onSwiper,
        onSlideChange,
      };
    },
  methods: {
    // initYouTubePlayer() { //影片嵌入相關
    //   if (window.YT && window.YT.Player) {
    //     // 替换为你的 YouTube 视频 ID
    //     const videoId = this.trailerLink;
    //     // 创建 YouTube 播放器
    //     new window.YT.Player(this.$refs.youtubePlayer, {
    //       height: "630",
    //       width: "1080",
    //       videoId: videoId,
    //       playerVars: { autoplay: 0 }, // 1 表示自动播放
    //     });
    //   } else {
    //     // 如果 'Player' 未定义，你可能需要等待 API 加载完成
    //     // 或者在其他地方处理 'onYouTubeIframeAPIReady' 事件
    //     console.error("YouTube API not ready");
    //   }
    // },
    async getPerson() { //電影相關 上映中 演員*5 + 導演*1
      const options = {
        method: "GET",
        headers: {
          accept: "application/json",
          Authorization:
            "Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiIxZTBiNGVhYWYyMjVhZTdmYzFhNjdjYzk0ODk5Mjk5OSIsInN1YiI6IjY1N2ZjYzAzMGU2NGFmMDgxZWE4Mjc3YSIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.3d6GcXTBf2kwGx9GzG7O4_8eCoHAjGxXNr9vV1lVXww",
        },
      };
      await fetch(
        `https://api.themoviedb.org/3/movie/${this.movieInfo.movieId}/credits?language=en-US`,
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
          this.directors = directors;
          this.casts = cast;
          console.log("全部電影的上映中 導演 objPerson", this.directors);
          console.log("全部電影的上映中 演員 objPerson", this.casts);
        })
        .catch((error) => {
          console.error(error);
        });
    },
    async getTrailer() { //預告片
      const options = {
        method: "GET",
        headers: {
          accept: "application/json",
          Authorization:
            "Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiIxZTBiNGVhYWYyMjVhZTdmYzFhNjdjYzk0ODk5Mjk5OSIsInN1YiI6IjY1N2ZjYzAzMGU2NGFmMDgxZWE4Mjc3YSIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.3d6GcXTBf2kwGx9GzG7O4_8eCoHAjGxXNr9vV1lVXww",
        },
      };
      try {
        const response = await fetch(
          `https://api.themoviedb.org/3/movie/${this.movieInfo.movieId}/videos?language=en-US`,
          options
        );
        if (!response.ok) {
          throw new Error("Network response was not ok");
        }
        const data = await response.json();
        const firstTrailerKey = data.results?.[0]?.key;
        this.trailerLink = `${firstTrailerKey}`;
        // console.log("firstTrailerKey", this.trailerLink);
      } catch (error) {
        console.error(error);
        return null; // 或者返回其他适当的值，视情况而定
      }
    },
    async getMovieType() { //電影類型 
        const options = {
        method: 'GET',
        headers: {
          accept: 'application/json',
          Authorization: 'Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiIxZTBiNGVhYWYyMjVhZTdmYzFhNjdjYzk0ODk5Mjk5OSIsInN1YiI6IjY1N2ZjYzAzMGU2NGFmMDgxZWE4Mjc3YSIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.3d6GcXTBf2kwGx9GzG7O4_8eCoHAjGxXNr9vV1lVXww'
        }
      };
      fetch('https://api.themoviedb.org/3/genre/movie/list?language=en', options)
        .then((response) => response.json())
        .then((response) => {
          this.type = response.genres,
          console.log(this.type)
          console.log(this.movieInfo.movieGenreid)
          console.log(this.movieInfo.movieGenreid.length)
          console.log(this.type.length)
          console.log(this.movieInfo.movieGenreid[0])
          console.log(this.type[6].id)
          console.log(parseInt(this.movieInfo.movieGenreid[0])===this.type[6].id ? 1:2)
          for(let i=0;i<this.movieInfo.movieGenreid.length;i++){
            for(let j=0;j<this.type.length;j++)
            if(parseInt(this.movieInfo.movieGenreid[i])===this.type[j].id){
              this.movieType.push(this.type[j].name)
            }
          }
          console.log(this.movieType)
        })
        .catch(err => console.error(err));
    },
    splitMovies() {
      const pageSize = 9;
      this.pages = [];
      for (let i = 0; i < this.mymovie.length; i += pageSize) {
        this.pages.push(this.mymovie.slice(i, i + pageSize));
      }
    },
    logincheck(){
        this.userLoggedIn = Cookies.get('userLoggedIn')
        if (this.userLoggedIn) {
          this.account = Cookies.get('account')
          let a = Cookies.get('account')
          Cookies.set('userLoggedIn', true, { expires: 7, path: '/' });
          Cookies.set('account', a, { expires: 7, path: '/' });
        }
      console.log(this.userLoggedIn)
    },
    async getMovieName() { //上映中
      const options = {
        method: "GET",
        headers: {
          accept: "application/json",
          Authorization: 'Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJkNTFmNDFjYjUxYWI2NmIzMjJkMGM1OGZkMDY1Y2I1YSIsInN1YiI6IjY1NThmNzFmMDgxNmM3MDBhYmJlNWQ3MCIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.RtMbqdUQUCfdqaLD5SoZ18e4PlSq9Ap4ShtGhmUMm10'
        },
      };

      let page = 1;
      let count = 30; //要抓的電影數
      let playingMovies = [];
      let targetA = this.target

      try {
        while (playingMovies.length < count) {
          const api = `https://api.themoviedb.org/3/search/movie?query=${this.target}&include_adult=false&language=${this.languageTarget}&page=${page}`;
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
        const playMovies = playingMovies.filter((movie) => movie.poster_path).slice(0, count).sort((a, b) => new Date(a.release_date) - new Date(b.release_date));
        this.objPlayMovies = playMovies;
        console.log('上映中 PlayMovies:', this.objPlayMovies);
      } catch (error) {
        console.error(error);
      }
  },
  goback(){
    this.$router.push("/mypageB")
  },
  getrandonpage(){
    fetch('https://spintbootmovie.zeabur.app/movie/mypage/searchA', {
        method: 'POST', // 這裡使用POST方法，因為後端是@PostMapping
        headers: {
          'Content-Type': 'application/json'
          },
        body: JSON.stringify({
        })
      })
      .then(response => response.json())
      .then(data => { // 處理返回的數據
        console.log(data);
        if(data.code == 200){
          const randomIndex = Math.floor(Math.random() * data.pageList.length);
          console.log(randomIndex)
          this.movieInfo = JSON.parse(data.pageList[randomIndex].favorit)
          this.mymovie = JSON.parse(data.pageList[randomIndex].accountMovieList)
          this.moviecomment = data.pageList[randomIndex].favoritComment
          this.account = data.pageList[randomIndex].account
          console.log(this.movieInfo)
          console.log(this.mymovie)
          console.log(this.moviecomment)
          }
      })
      .catch(error => {
        console.error('Error fetching data:', error);
      });
  },
  searchmypageaccount(){
    
    console.log(this.account)
    fetch('https://spintbootmovie.zeabur.app/movie/mypage/search'+ '?' + "account=" + this.searchaccount, {
        method: 'POST', // 這裡使用POST方法，因為後端是@PostMapping
        headers: {
          'Content-Type': 'application/json'
          },
        body: JSON.stringify({
        })
      })
      .then(response => response.json())
      .then(data => { // 處理返回的數據
        console.log(data);
        if(data.code == 200){
          this.movieInfo = JSON.parse(data.mypageList.favorit)
          this.mymovie = JSON.parse(data.mypageList.accountMovieList)
          this.moviecomment = data.mypageList.favoritComment
          // console.log(this.movieInfo)
          // console.log(this.mymovie)
          console.log(this.moviecomment)
          this.$router.push({
            name: "mypage",
            query: {
              movieInfo: JSON.stringify(this.movieInfo),
              mymovie: JSON.stringify(this.mymovie),
              moviecomment: JSON.stringify(this.moviecomment),
            },
          });
          setTimeout(() => {
            this.movieType = []
            this.getMovieType();
            this.getPerson();
          }, 500);
          setTimeout(() => {
            this.getTrailer();
          }, 1000);
          setTimeout(() => {
            this.splitMovies();
          }, 1000);
          this.$nextTick(() => {
                var swiper = new Swiper(this.$refs.mySwiper, {
                  slidesPerView: 3,
                  slidesPerColumn: 3,
                  spaceBetween:10,
                  autoplay: {
                    delay: 3000,
                    disableOnInteraction: false,
                    stopOnLastSlide: false,
                  },
                  loop: false,
                  observer: true,
                  observeParents: true,
                });
              })
          this.logincheck()
          this.account = this.searchaccount
          }
      })
      .catch(error => {
        console.error('Error fetching data:', error);
      });
  },
  logaccount(x){
    console.log(x)
    fetch('https://spintbootmovie.zeabur.app/movie/mypage/search'+ '?' + "account=" + x, {
        method: 'POST', // 這裡使用POST方法，因為後端是@PostMapping
        headers: {
          'Content-Type': 'application/json'
          },
        body: JSON.stringify({
        })
      })
      .then(response => response.json())
      .then(data => { // 處理返回的數據
        console.log(data);
        if(data.code == 200){
          this.movieInfo = JSON.parse(data.mypageList.favorit)
          this.mymovie = JSON.parse(data.mypageList.accountMovieList)
          this.moviecomment = data.mypageList.favoritComment
          // console.log(this.movieInfo)
          // console.log(this.mymovie)
          console.log(this.moviecomment)
          this.$router.push({
            name: "mypage",
            query: {
              movieInfo: JSON.stringify(this.movieInfo),
              mymovie: JSON.stringify(this.mymovie),
              moviecomment: JSON.stringify(this.moviecomment),
            },
          });
          setTimeout(() => {
            this.movieType = []
            this.getMovieType();
            this.getPerson();
          }, 500);
          setTimeout(() => {
            this.getTrailer();
          }, 1000);
          setTimeout(() => {
            this.splitMovies();
          }, 1000);
          this.$nextTick(() => {
                var swiper = new Swiper(this.$refs.mySwiper, {
                  slidesPerView: 3,
                  slidesPerColumn: 3,
                  spaceBetween:10,
                  autoplay: {
                    delay: 3000,
                    disableOnInteraction: false,
                    stopOnLastSlide: false,
                  },
                  loop: false,
                  observer: true,
                  observeParents: true,
                });
              })
          this.logincheck()
        } else if(data.code == 400){
          this.getrandonpage()
        }
      })
      .catch(error => {
        console.error('Error fetching data:', error);
      });
  },
  chooseMovie(item){
    this.$router.push({
        name: 'moviecomment',
        query: { 
          movieGenreid: item.movieGenreid,
          movieId: item.movieId,
          movieOriginaltitle: item.movieOriginaltitle, 
          movieTitle: item.movieTitle, 
          movieOverview: item.movieOverview, 
          moviePoster: item.moviePoster, 
          movieBack: "", 
          movieReleasedate: item.movieReleasedate, 
          movieVoteavg: item.movieVoteavg, 
        }
      });
  },
},
  beforeMount(){
    this.logincheck()
    if (this.account !="") {
      this.logaccount(this.account)
      console.log("login check")
    }
  },
  async mounted() {
    if(Object.keys(this.$route.query).length !== 0){
      console.log("A")
      console.log(this.$route.query)
      this.movieInfo = this.$route.query.movieInfo
      this.mymovie = this.$route.query.mymovie
      this.moviecomment = this.$route.query.moviecomment
    } else if (this.account =="") {
      console.log("B")
      this.getrandonpage()
    }
    setTimeout(() => {
      this.getMovieType();
      this.getPerson();
    }, 500);
    setTimeout(() => {
      this.getTrailer();
    }, 1000);
    setTimeout(() => {
      this.splitMovies();
    }, 1000);
    this.$nextTick(() => {
          var swiper = new Swiper(this.$refs.mySwiper, {
            slidesPerView: 3,
            slidesPerColumn: 3,
            spaceBetween:10,
            autoplay: {
              delay: 3000,
              disableOnInteraction: false,
              stopOnLastSlide: false,
            },
            loop: false,
            observer: true,
            observeParents: true,
          });
        })
    this.logincheck()
    },
};
</script>

<template>
  <!-- <div class="loader">
    <div class="loadingio-spinner-rolling-3hvvs6i9c3b">
      <div class="ldio-b9el9z8mymt">
        <h1>請稍後......</h1>
        <div></div>
      </div>
    </div>
  </div> -->

  <div class="body">
    <!-- 電影資料 -->
    <div class="header">
      <!-- <button type="button" @click="goback">去後台</button> -->
      <div class="toppet" style="">
        <p class="textHeader" style="">目前所在個人主頁帳號：{{ this.account }}</p>
        <div class="searchaccount" style="">
            <input class="inputSearch" type="text" name="" id="" v-model="this.searchaccount" style="">
            <button class="inputButton button" type="button" @click="searchmypageaccount()"  style="">以帳號搜尋個人頁</button>
        </div>
      </div>
      <div class="movieData">
        <!-- <img :src="'https://image.tmdb.org/t/p/w342' + this.movieInfo.movieBack " alt="" style="width: 100vw; height: 100vh; opacity: 0.2; position: fixed; top: 0; left: 0;"><br> -->
        <div class="movieDataLeft">
          <a @click="chooseMovie(this.movieInfo)">
            <img class="imgLeft" :src="'https://image.tmdb.org/t/p/w500' + this.movieInfo.moviePoster" alt=""/>
          </a>
        </div>
        <div class="movieDataRight">
          <h1 class="textHeader">{{ this.movieInfo.movieTitle }}</h1>
          <h6 class="textall">{{ this.movieInfo.movieOriginaltitle }}</h6>
          <h2 class="textHeader">上映日期：{{ this.movieInfo.movieReleasedate }}</h2>
          <hr />
          <h2>Movie Info</h2>
          <div class="movieDataRight1">
            <div class="movieDataRight22">
              <div class="type">
                <h3 class="textHeader typeH3">類型：</h3>
                <span class="textall" style="" v-for="(item,index) in this.movieType" :key="index">{{ item }}<span v-if="index < this.movieType.length - 1" class="textall" style="font-size: 1em;">、</span></span><br>
              </div>
              <div class="director">
                <h3 class="textHeader">導演：</h3>
                <span class="textall" style="" v-for="(item, index) in this.directors" :key="index">{{ item.original_name }}<span v-if="index < this.directors.length - 1">,</span></span><br>
              </div>
              <div class="casts">
                <h3 class="textHeader" style="">演員：</h3>
                <div style="width: 50%;display: flex;">
                  <p class="textall" style="" v-for="(item, index) in this.casts" :key="index">{{ item.original_name }}<span v-if="index < this.casts.length - 1" class="textall" style="font-size: 1em;">、</span></p><br></div>
              </div>
              <div class="voteAvg">
                <h3 class="textHeader">評分：</h3>
                <h2 class="textall" style="">{{ this.movieInfo.movieVoteavg }}</h2>
              </div>
              <div class="movieOverview">
                <h3 class="textHeader" style="">簡介：</h3>
                <p class="textallx2" v-if="this.movieInfo.movieOverview" style="">{{ this.movieInfo.movieOverview }}</p>
                <p class="textall" v-else>此電影無簡介</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <hr class="hrLine2" />
    <!-- 預告片 -->
    <h1 class="textTilte">個人影評</h1>
    <p class="text" style="">{{ this.moviecomment }}</p>
    <div class="middle">
      <!-- <h1>預告片</h1> -->
      <!-- <video :src="this.trailerLink" controls></video> -->
      <!-- <iframe :src="this.trailerLink" controls></iframe>-->
      <!-- <div ref="youtubePlayer"></div> -->
      <iframe class="middleIframe"  :src="'https://www.youtube.com/embed/' + trailerLink" frameborder="0" allowfullscreen></iframe>
    </div>
    <!-- 討論區 -->

  <hr />
    <div class="footer" ref="scheduleSwipers">
      <h1 class="textTilte">我的推薦電影</h1>
      <swiper :options="swiperOption" style="" ref="mySwiper">
        <swiper-slide v-for="(page, index) in pages" :key="index">
          <div class="grid-container">
            <div class="grid-item" v-for="(movie, i) in page" :key="i">
              <a @click="chooseMovie(movie)">
                <img :src="'https://image.tmdb.org/t/p/w500' + movie.moviePoster" class="gridImg" alt="" @click="">
                <div class="textallx">{{ movie.movieTitle }}</div>
              </a>
            </div>
          </div>
        </swiper-slide>
      </swiper>
    </div>
  </div>
</template>

<style scoped lang="scss">
.loader {
  //又報錯
  background: linear-gradient(90deg, #b3fffd 0, #e3e6ff 50%, #fde5f5 100%);
  background-size: 200% 200%;
  background-color: white;
  // opacity: 0.8;
  width: 100vw;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  position: fixed;
  z-index: 100000;
  background-position: center;
  background-repeat: no-repeat;
  // background-size: 65vw 70vh;
  top: 0;
  left: 0;
  pointer-events: none;
  animation: bgGrade 10s ease infinite;
}
.ldio-b9el9z8mymt div {
  //轉動齒輪
  position: absolute;
  width: 120px;
  height: 120px;
  border: 20px solid #af3fc8;
  border-top-color: transparent;
  border-radius: 50%;
  opacity: 0.5;
}
.ldio-b9el9z8mymt div {
  animation: ldio-b9el9z8mymt 1s linear infinite;
  top: 100px;
  left: 100px;
}
.loadingio-spinner-rolling-3hvvs6i9c3b {
  width: 200px;
  height: 200px;
  display: inline-block;
  overflow: hidden;
}
.ldio-b9el9z8mymt {
  width: 100%;
  height: 100%;
  position: relative;
  transform: translateZ(0) scale(1);
  backface-visibility: hidden;
  transform-origin: 0 0;
}
.ldio-b9el9z8mymt div {
  box-sizing: content-box;
}
@keyframes ldio-b9el9z8mymt {
  0% {
    transform: translate(-50%, -50%) rotate(0deg);
  }
  100% {
    transform: translate(-50%, -50%) rotate(360deg);
  }
}
span, button, p, label, select {
  font-family: "Montserrat", sans-serif, sans-serif, "M PLUS 1";
  color: #557;
  font-size: 18px;
}
small, h1, h2, h3, h4, h5, h6 {
  font-family: "Montserrat", sans-serif, sans-serif, "M PLUS 1";
  color: #557;
}
h1, h2, h3, h4, h5, h6 {
  font-family: "Montserrat", sans-serif, sans-serif, "M PLUS 1";
  color: #557;
  line-height: 7dvh;
}
span, button {
  margin: 10px 10px 10px 0;
}
.col-md-8 {
  width: 50vw;
  margin: 0 auto;
  align-items: start;
  justify-content: start;
  text-align: start;
}
.card {
  margin-bottom: 10px;

  .card-body {
    flex-direction: column;
    align-items: flex-start;
  }
  .btn-link {
    padding: 0;
    margin-right: 10px;
    cursor: pointer;
  }

  .btn-primary,
  .btn-outline-primary {
    margin-right: 10px;
  }

  .text-muted {
    margin-left: auto;
  }

  textarea {
    resize: vertical;
  }

  .mt-2 {
    margin-top: 10px;
  }
}
.body {
  width: 100%;
  height: 260vh;

  .header {
    width: 100%;
    // height: 90dvh;
    margin: 0 auto;
    min-height: 125dvh;
    // margin-bottom: 1dvh;
    // padding-top: 20px;

    .toppet{
      width: 100%;
      height: 12dvh;
      display: flex;
      margin-top: 2dvh;
      .textHeader{
        margin: 1% 4% 1% 1%; 
        min-width: 30%; 
        height: 8dvh;
        background-color: rgb(176, 182, 213); 
        border-radius: 20px;
        line-height: 8dvh;
        margin-left: 6%;
      }

      .searchaccount{
        width: 100%;
        display: flex;
        .inputSearch{
          height: 7dvh;
          width: 20%;
          margin: auto 0 auto 0; 
          border-radius: 5px;
          margin-left: 5.5%;
        }
        .inputButton{
          min-width: 25%; 
          height: 7dvh;
          margin: auto 0 auto 2%;
        }

      }
    }
    .movieData {
      display: flex;
      .movieDataLeft {
        width: 42%;

        .imgLeft{
          width: 80%;
          height: 100dvh;
          // margin-right: 13%;
          margin-top: 3dvh;
        }

      }
      .movieDataRight {
        width: 55%;
        // height: 90dvh;
        text-align: start;
        align-items: start;
        margin-left: 1%;
        .movieDataRight1{
          width: 100%;
          height: 20dvh;
          display: flex;
            .movieDataRight11{
              width: 10%;
              height: 40dvh;
              text-align: start;
              align-items: start;
            }
            .movieDataRight22{
              width: 90%;
              height: 40dvh;
              text-align: start;
              align-items: start;
              .type{
                display: flex;
                margin-bottom: 2dvh;
                width: 100%;
              }
              .director{
                display: flex;
                margin-bottom: 2dvh;
              }
              .casts{
                display: flex;
                margin-bottom: 2dvh;
              }
              .voteAvg{
                display: flex;
                margin-bottom: 2dvh;
              }
              .movieOverview{
                display: flex;
                margin-bottom: 2dvh;
                width: 90%;
                height: 40dvh;
              }
          }
        }
      }
    }
  }
  .middle {
    width: 100%;
    min-height: 90dvh;
    // margin: 0 auto;

    .middleIframe{
      width:80%; 
      height:90dvh;
    }
  }
  .commentArea {
    width: 95%;
    height: 30dvh;
    margin: 0 auto;
  }
  .footer{
    width: 100%;
    // height: 60dvh;
    margin: 0 auto;
    min-height: 200dvh;
  }
}

// .hrLine2{
//   margin-top: 1dvh;
// }

.textTilte{
  font-family:'jf-openhuninn-2.0';
  font-size: 4em;
  margin: 8dvh 0 7dvh 0;
}
.text{
  font-family:'jf-openhuninn-2.0';
  font-size: 2em;
  width: 80%;
  margin: 0 auto 0 auto;
  margin-bottom: 6dvh;
}
.textall{
  font-family:'jf-openhuninn-2.0';
  font-size: 1.5em;
  margin: 0;
  line-height: 5dvh;
  margin-top: 1dvh;
  // width: 80%;

}
.textallcast{
  font-family:'jf-openhuninn-2.0';
  font-size: 1em;
  margin: 0;
}
.textallx{
  font-family:'jf-openhuninn-2.0';
  font-size: 1.2em;
  // margin: 0;
  overflow: auto;  /* 或者使用 overflow: scroll; */
  max-height: 35dvh;  /* 设置最大高度，超出部分会产生滚动条 */
  // white-space: nowrap;  /* 防止文本换行 */
  // line-height: 5dvh;
  width: 85%;
  height: 10dvh;
  margin-top: 1dvh;
  margin-left: 6%;
  // margin-bottom: 5dvh;

}

.textallx2{
  font-family:'jf-openhuninn-2.0';
  font-size: 1.2em;
  margin: 0;
  overflow: auto;  /* 或者使用 overflow: scroll; */
  max-height: 35dvh;  /* 设置最大高度，超出部分会产生滚动条 */
  // white-space: nowrap;  /* 防止文本换行 */
  // line-height: 5dvh;
  width: 85%;
  margin-top: 1dvh;
}

.textHeader{
  font-family:'jf-openhuninn-2.0';
  font-size: 1.5em;
  margin: 0;
}


.grid-container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-gap: 1%;
  margin-bottom: 7dvh;

}

.grid-item {
  /* Add your custom styles for each grid item here */
  // height: 120dvh;
  // height: 110dvh;

  
  a{
    .gridImg{
      width: 95%;
      height: 100dvh;
      // margin-right: 10%;
      margin-left: 1%;
      margin-right: 1%;
    }
  }
}

.button{
        min-width: 5%;
        height: 5.9dvh;
        border: none;
        background-color: rgb(176, 182, 213);
        border-radius: 10px;
        font-size: 1.5em;
        font-family:'jf-openhuninn-2.0';
        margin-top: 1dvh;
        margin-left: 2%;
    }


    @media (max-width: 767px){



      .loader {
  
}
.ldio-b9el9z8mymt div {
  
}
.ldio-b9el9z8mymt div {
  
}
.loadingio-spinner-rolling-3hvvs6i9c3b {
  
}
.ldio-b9el9z8mymt {
  
}
.ldio-b9el9z8mymt div {
  
}
@keyframes ldio-b9el9z8mymt {
  0% {
    
  }
  100% {
    
  }
}
span, button, p, label, select {
  
}
small, h1, h2, h3, h4, h5, h6 {
  
}
h1, h2, h3, h4, h5, h6 {
  
}

h2{
  margin-left: 2%;
}

span, button {
  
}
.col-md-8 {
  
}
.card {
  

  .card-body {
    
  }
  .btn-link {
    
  }

  .btn-primary,
  .btn-outline-primary {
    
  }

  .text-muted {
    
  }

  textarea {
    
  }

  .mt-2 {
    
  }
}
.body {
  

  .header {
    // min-height: 150dvh;

    .toppet{
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      .textHeader{
        font-size: 1em;
        min-width: none;
        width: 55%;
        height: 17dvh;
        line-height: 5dvh;
        margin: 0;
        margin-top: 8dvh;
        padding: 10px;
        // margin-left: 1.5%;
         

        
      }

      .searchaccount{
        margin-top: 2dvh;
        justify-content: center;
        
        .inputSearch{
          height: 5dvh;
          width: 30%;
          margin-left: 4%;
        }
        .inputButton{
          // min-width: 5%; 
          // height: 4dvh;
        }

      }
    }
    .movieData {
      display: flex;
      flex-direction: column;
      .movieDataLeft {
        width: 100%;
        margin-top: 9dvh;

        .imgLeft{
          width: 50%;
          height: 38dvh;
        }

      }
      .movieDataRight {
        width: 96%;
        
        
        .movieDataRight1{
          height: 85dvh;
            .movieDataRight11{
              
            }
            .movieDataRight22{
              height: 85dvh;
              width: 100%;
              
              .type{
                
              }
              .director{
                
              }
              .casts{
                
              }
              .voteAvg{
                
              }
              .movieOverview{
                
              }
          }
        }
      }
    }
  }
  .middle {
    min-height: 30dvh;

    .middleIframe{
      width:80%; 
      height:30dvh;
    }
  }
  .commentArea {
    
    
  }
  .footer{
    min-height: 80dvh;
  }
}

.textTilte{
  margin-top: 10dvh;
  font-size: 2em;

}
.text{
  font-size: 1.2em;
  
}
.textall{
  font-size: 0.9em;
  margin-left: 0%;
  width: 40%;
}

.typeH3{
  width: 30%;
}

.textallcast{
  
}
.textallx{
  height: 13dvh;

}

.textallx2{
  width: 80%;
  font-size: 1em;
  margin-top: 2dvh;

  
}

.textHeader{
  font-size: 1em;
  margin-left: 1%;
}


.grid-container {
  margin-bottom: 4dvh;
  
}

.grid-item {
  

  
  a{
    .gridImg{
      height: 25dvh;
    }
  }
}

.button{
        min-width: 3%;
        height: 5dvh;
        font-size: 1em;
        margin-top: 0dvh;
    }

    }


</style>
