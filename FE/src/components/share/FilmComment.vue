<template lang="">
  <div class="film-share__comment-area">
    <div class="film-share__comment-list" ref="commentsArea">
      <FilmCommenntItem
        v-for="comment in comments"
        :key="comment.commentId"
        :comment="comment"
        @update-comment-list="updateCommentList"
      ></FilmCommenntItem>
    </div>

    <div class="borderline"></div>
    <div class="input_container">
      <div class="smile"><smile></smile></div>
      <input
        type="text"
        v-model="inputComment"
        @keyup.enter="sendComment"
        aria-labelledby="firstname"
      />
      <div class="send" @click="sendComment"><send></send></div>
    </div>
  </div>
</template>
<script>
import { ref, watch, nextTick } from "vue";
import smile from "@/assets/icons/smile.svg";
import send from "@/assets/icons/send.svg";
import FilmCommenntItem from "@/components/share/FilmCommentItem.vue";
import { postComment } from "@/api/comment";
import { useStore } from "vuex";

export default {
  components: {
    smile,
    send,
    FilmCommenntItem,
  },
  props: {
    comments: Array,
    articleId: Number,
  },
  emits: ["update-comment-list"],
  setup(props, { emit }) {
    const store = useStore();
    const inputComment = ref(null);
    const updateCommentList = () => {
      emit("update-comment-list");
    };
    const sendComment = () => {
      if (inputComment.value) {
        postComment(
          {
            userId: store.state.user.userId,
            articleId: props.articleId,
            commentContents: inputComment.value,
          },
          () => {
            inputComment.value = null;
            updateCommentList();
          },
          (error) => {
            console.log("댓글 등록 에러", error);
          }
        );
      }
    };
    const commentsArea = ref(null);
    watch(
      () => props.comments,
      (newList, oldList) => {
        if (newList.length > oldList.length) {
          nextTick(() => {
            commentsArea.value.scrollTo({
              top: commentsArea.value.scrollHeight,
              behavior: "smooth",
            });
          });
        }
      },
      { deep: true }
    );
    return {
      inputComment,
      sendComment,
      updateCommentList,
      commentsArea,
    };
  },
};
</script>
<style scoped lang="scss">
.post_comment {
  display: flex;
  flex-direction: column;
  height: 20px;
  // min-height: 100%;
  max-height: 250px;
  // height: 40%;
  margin-top: 16px;
  padding: 16px 3px 0 7px;
  border-top: 1px solid gray;
  // background-color: coral;
  // box-shadow: 0 0 0 1px #000 inset;
}

.film-share__comment-list {
  height: 284px;
  overflow: auto;
}
.comment_list {
  display: flex;
  align-items: center;
  margin-bottom: 16px;

  img {
    width: 36px;
    height: 36px;
    border-radius: 50%;
  }

  .comment_nickname {
    margin-left: 24px;
    margin-right: 24px;
  }
}

.borderline {
  border: none;
}

.input_container {
  display: flex;
  text-align: center;
  align-items: center;
  box-sizing: border-box;
  height: 48px;
  padding-left: 16px;
  padding-right: 16px;
  margin-top: 3px;

  .smile {
    margin-left: 8px;
    margin-right: 16px;
  }

  .send {
    margin-left: 16px;
    margin-right: 8px;
  }

  input {
    width: 100%;
    height: 80%;
    padding-left: 16px;
    padding-right: 16px;
    box-sizing: border-box;
    border: 0;
    outline: 0;
    border-radius: 15px;
    background-color: rgb(233, 233, 233);
  }
}
</style>
