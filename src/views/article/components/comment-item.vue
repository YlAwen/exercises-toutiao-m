<template>
  <van-cell class="comment-item">
    <van-image
      slot="icon"
      class="avatar"
      round
      fit="cover"
      :src="comment.aut_photo"
    />
    <div slot="title" class="title-wrap">
      <div class="user-name">{{ comment.aut_name }}</div>
      <van-button
        @click="onCommentLike"
        class="like-btn"
        :loading="commentLoading"
        :class="{
          liked: comment.is_liking,
        }"
        :icon="comment.is_liking ? 'good-job' : 'good-job-o'"
        >{{ comment.like_count > 0 ? comment.like_count : "赞" }}</van-button
      >
    </div>

    <div slot="label">
      <p class="comment-content">{{ comment.content }}</p>
      <div class="bottom-info">
        <span class="comment-pubdate">{{
          comment.pubdate | relativeTime
        }}</span>
        <van-button
          class="reply-btn"
          round
          @click="$emit('replay-click', comment)"
          >回复 {{ comment.reply_count }}</van-button
        >
      </div>
    </div>
  </van-cell>
</template>

<script>
export default {
  name: "CommentItem",
  components: {},
  props: {
    comment: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      commentLoading: false,
    };
  },
  computed: {},
  watch: {},
  created() {},
  mounted() {},
  methods: {
    async onCommentLike() {
      this.commentLoading = true;
      try {
        if (this.comment.is_liking) {
          // 已点赞，取消点赞
          const res = await this.axios.delete(
            `/v1_0/comment/likings/${this.comment.com_id}`
          );
          // console.log(res);
          this.comment.like_count--;
        } else {
          // 未点赞，进行点赞
          const res = await this.axios.post("/v1_0/comment/likings", {
            target: this.comment.com_id,
          });
          // console.log(res);
          this.comment.like_count++;
        }
        this.comment.is_liking = !this.comment.is_liking;
      } catch (error) {
        this.$toast("操作失败，请重试");
      }
      this.commentLoading = false;
    },
  },
};
</script>

<style scoped lang="less">
.comment-item {
  .avatar {
    width: 36px;
    height: 36px;
    margin-right: 13px;
  }
  .title-wrap {
    display: flex;
    justify-content: space-between;
    align-items: center;
    .user-name {
      color: #406599;
      font-size: 13px;
    }
  }
  .comment-content {
    font-size: 16px;
    color: #222222;
    word-break: break-all;
    text-align: justify;
  }
  .comment-pubdate {
    font-size: 10px;
    color: #222;
    margin-right: 13px;
  }
  .bottom-info {
    display: flex;
    align-items: center;
  }
  .reply-btn {
    // width: 68px;
    height: 24px;
    line-height: 24px;
    font-size: 11px;
    color: #222;
  }
  .like-btn {
    height: 15px;
    padding: 0;
    border: none;
    font-size: 10px;
    line-height: 15px;
    margin-right: 4px;
    .van-icon {
      font-size: 15px;
    }
    &.liked {
      color: #e5645f;
    }
  }
}
</style>
