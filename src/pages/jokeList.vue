<template>
  <div class="joke-root">
    <template>
      <el-tabs class="tabs" v-model="activeName" @tab-click="handleClick">
        <el-tab-pane label="全部" name="全部"></el-tab-pane>
        <el-tab-pane label="要闻" name="要闻"></el-tab-pane>
        <el-tab-pane label="财经" name="财经"></el-tab-pane>
        <el-tab-pane label="环球" name="环球"></el-tab-pane>
        <el-tab-pane label="娱乐" name="娱乐"></el-tab-pane>
        <el-tab-pane label="体育" name="体育"></el-tab-pane>
      </el-tabs>
    </template>
    <div v-if="isShowBanner" class="banner">
      <el-carousel height="250px">
        <el-carousel-item v-for="ban in bannerData" :key="ban">
          <!-- <a :href="ban.articleUrl" target="_blank"> -->
          <span class="banner-title">{{ ban.title }}</span>
          <el-image class="banner-ic" :src="ban.imgUrl" :fit="fit"></el-image>
          <!-- </a> -->
        </el-carousel-item>
      </el-carousel>
    </div>
    <jokeItem v-for="item in tableData" :bean="item" :key="item"></jokeItem>
    <more v-on:loadMore="loadMore" :showInfo="showInfo"></more>
  </div>
</template>
<script>
import jokeItem from "@/components/jokeItem";
import more from "@/components/more";
import { JOKE_CATEGORY, JOKE_TAGS } from "@/config/env";
export default {
  components: {
    jokeItem,
    more,
  },
  data() {
    return {
      tableData: [],
      count: 0,
      bannerData: [
        {
          id: 1,
          title: "大海",
          imgUrl: "https://cdn.pixabay.com/photo/2022/06/30/20/10/lake-7294456_960_720.jpg",
        },
        {
          id: 2,
          title: "房屋",
          imgUrl: "https://cdn.pixabay.com/photo/2023/02/04/09/20/castle-7766794_960_720.jpg",
        },
        {
          id: 3,
          title: "鸟",
          imgUrl: "https://cdn.pixabay.com/photo/2023/02/13/18/00/bird-7787970_960_720.jpg",
        },
        {
          id: 4,
          title: "宗教",
          imgUrl: "https://cdn.pixabay.com/photo/2023/01/23/23/20/altar-7739897_960_720.jpg",
        },
        {
          id: 5,
          title: "蒲公英",
          imgUrl: "https://cdn.pixabay.com/photo/2023/01/05/22/35/flower-7700011_960_720.jpg",
        },
      ],
      page: 1,
      row: 10,
      activeName: "-1",
      isShowBanner: true,
      fit: "cover",
      showInfo: "点击加载更多",
    };
  },
  created() {
    this.getArticleList();
  },
  mounted() {
    var _this = this;
    this.getJokes("全部");
    if (this.isShowBanner) {
      this.getBanners();
    }
  },
  methods: {
    getJokes(page) {
      this.showInfo = "加载中..";
      if (page === "全部") {
        this.getArticleList();
        this.isShowBanner = true;
      } else {
        this.getArticleByCateGory(page);
      }
    },
    getArticleList() {
      this.$axios
        .get("/article/jokedetaillist")
        .then((response) => {
          const joker = response.data;
          const data = joker.data.records;
          // this.tableData = [];
          if (data.length < this.row) {
            this.showInfo = "没有数据了~";
          } else {
            this.showInfo = "点击加载更多";
          }
          data.forEach((item) => {
            const tableData = {};
            tableData.articleCommentCount = item.articleCommentCount;
            tableData.articleLikeCount = item.articleLikeCount;
            tableData.content = item.content;
            tableData.contentHtml = item.contentHtml;
            tableData.coverImg = item.coverImg;
            tableData.jokeId = item.jokeId;
            tableData.jokeUserIcon = item.jokeUserIcon;
            tableData.jokeUserId = item.jokeUserId;
            tableData.jokeUserNick = item.jokeUserNick;
            tableData.postTime = item.postTime;
            tableData.title = item.title;
            if (item.category) {
              tableData.category = JOKE_CATEGORY[item.category];
            } else {
              tableData.category = JOKE_CATEGORY["0"];
            }
            if (item.tags) {
              tableData.tags = JSON.parse(item.tags);
            } else {
              tableData.tags = ["0"];
            }

            this.tableData.push(tableData);
          });
        })
        .catch((err) => {});
    },
    getArticleByCateGory(page) {
      this.$axios
        .get(`/article/searchWithCategory`, {
          params: {
            category: page,
          },
        })
        .then((response) => {
          const joker = response.data;
          const data = joker.data;
          // this.tableData = [];
          if (data.length < this.row) {
            this.showInfo = "没有数据了~";
          } else {
            this.showInfo = "点击加载更多";
          }
          data.forEach((item) => {
            const tableData = {};
            tableData.articleCommentCount = item.articleCommentCount;
            tableData.articleLikeCount = item.articleLikeCount;
            tableData.content = item.content;
            tableData.contentHtml = item.contentHtml;
            tableData.coverImg = item.coverImg;
            tableData.jokeId = item.jokeId;
            tableData.jokeUserIcon = item.jokeUserIcon;
            tableData.jokeUserId = item.jokeUserId;
            tableData.jokeUserNick = item.jokeUserNick;
            tableData.postTime = item.postTime;
            tableData.title = item.title;
            if (item.category) {
              tableData.category = JOKE_CATEGORY[item.category];
            } else {
              tableData.category = JOKE_CATEGORY["0"];
            }
            if (item.tags) {
              tableData.tags = JSON.parse(item.tags);
            } else {
              tableData.tags = ["0"];
            }

            this.tableData.push(tableData);
          });
        })
        .catch((error) => {
          console.log(error);
        });
    },
    getBanners() {
      this.$axios
        .get(`/banner/list`)
        .then((response) => {
          const banner = response.data;
          const data = banner.data;
          this.count = banner.total;
          this.bannerData = [];
          data.forEach((item) => {
            const bannerData = {};
            bannerData.imgUrl = item.imgUrl;
            bannerData.upTime = item.upTime;
            bannerData.title = item.title;
            bannerData.articleUrl = item.articleUrl;
            bannerData.articelId = item.articelId;
            this.bannerData.push(bannerData);
          });
        })
        .catch((error) => {
          console.log(error);
        });
    },
    handleClick(tab, event) {
      if (this.activeName === "1") {
        this.isShowBanner = true;
      } else {
        this.isShowBanner = false;
      }
      this.tableData = [];
      this.page = 1;
      this.getJokes(this.activeName);
      if (this.isShowBanner) {
        this.getBanners();
      }
    },
    loadMore() {
      this.page++;
      this.getJokes(this.activeName);
    },
    openToast(msg) {
      this.$notify.error({
        title: "错误",
        message: msg,
      });
    },
  },
};
</script>
<style lang="scss">
.joke-root {
  width: 800px;
  height: 100%;
  background-color: white;

  .tip1 {
    display: block;
    font-size: 16px;
    padding: 10px 10px;
    color: #666;
    border-bottom: 1px solid #f0f0f0;
  }
}

.tabs {
  padding: 0 10px;
}

.banner {
  padding: 10px;
  color: #475669;

  .banner-ic {
    width: 100%;
    height: 250px;
  }

  .banner-title {
    position: absolute;
    width: 160px;
    bottom: 10px;
    right: 10px;
    color: #eee;
    font-size: 14px;
    z-index: 2;
    white-space: nowrap;
    text-overflow: ellipsis;
    overflow: hidden;
    text-align: right;
  }
}
</style>
