<script setup>
import { ref, watch, onMounted } from "vue";
import { useRoute, useRouter } from "vue-router";
import { registArticle, getModifyArticle, modifyArticle } from "@/api/board";

// (getUserInformation) 로그인 정보를 받아오기 위한 필드와 메소드
import { userMyPage } from "@/api/user";
import { jwtDecode } from "jwt-decode";

// const user_id = ref("");
// const user_password = ref();
// const user_regDate = ref();
// const refreshToken = ref();
// const user_account = ref("");
// const user_role = ref("");
// const user_name = ref("");
// const user_phone = ref("");
// const user_cash = ref("");
// const user_mileage = ref("");

onMounted(() => {
  // 페이지 로드전 객체를 불러온다.
  getUserInformation();
});

const getUserInformation = () => {
  let token = sessionStorage.getItem("accessToken");
  let decodeToken = jwtDecode(token);
  userMyPage(
    decodeToken.userId,
    ({ data }) => {
      board.value.user_id = data.resdata.user_id;
    },
    (error) => {
      console.log(error);
    }
  );
};
///

const router = useRouter();
const route = useRoute();

const props = defineProps({ type: String });

const isUseId = ref(false);

const board = ref({
  board_id: 0,
  user_id: "",
  board_title: "",
  board_content: "",
  board_views: 0,
  board_regDate: "",
});

if (props.type === "modify") {
  let { board_id } = route.params;
  console.log(board_id + "번글 얻어와서 수정할거야");
  getModifyArticle(
    board_id,
    ({ data }) => {
      board.value = data;
      isUseId.value = true;
    },
    (error) => {
      console.log(error);
    }
  );
  isUseId.value = true;
}

const subjectErrMsg = ref("");
const contentErrMsg = ref("");
watch(
  () => board.value.board_title,
  (value) => {
    let len = value.length;
    if (len == 0 || len > 30) {
      subjectErrMsg.value = "제목을 확인해 주세요!!!";
    } else subjectErrMsg.value = "";
  },
  { immediate: true }
);
watch(
  () => board.value.board_content,
  (value) => {
    let len = value.length;
    if (len == 0 || len > 500) {
      contentErrMsg.value = "내용을 확인해 주세요!!!";
    } else contentErrMsg.value = "";
  },
  { immediate: true }
);

function onSubmit() {
  // event.preventDefault();

  if (subjectErrMsg.value) {
    alert(subjectErrMsg.value);
  } else if (contentErrMsg.value) {
    alert(contentErrMsg.value);
  } else {
    props.type === "regist" ? writeArticle() : updateArticle();
  }
}

function writeArticle() {
  console.log("글등록하자!!", board.value);
  registArticle(
    board.value,
    (response) => {
      let msg = "글등록 처리시 문제 발생했습니다.";
      if (response.status == 201) msg = "글등록이 완료되었습니다.";
      alert(msg);
      moveList();
    },
    (error) => console.log(error)
  );
}

function updateArticle() {
  console.log(board.value.articleNo + "번글 수정하자!!", board.value);
  modifyArticle(
    board.value,
    (response) => {
      let msg = "글수정 처리시 문제 발생했습니다.";
      if (response.status == 200) msg = "글정보 수정이 완료되었습니다.";
      alert(msg);
      moveList();
      // router.push({ name: "article-view" });
      // router.push(`/board/view/${article.value.articleNo}`);
    },
    (error) => console.log(error)
  );
}

function moveList() {
  router.push({ name: "board-list" });
}
</script>

<template>
  <form @submit.prevent="onSubmit">
    <!-- <div class="mb-3">
      <label for="user_id" class="form-label">작성자 ID : </label>
      <input
        type="text"
        class="form-control"
        v-model="board.user_id"
        :disabled="isUseId"
        placeholder="작성자ID..."
      />
    </div> -->
    <div class="mb-3">
      <label for="board_title" class="form-label">제목 : </label>
      <input type="text" class="form-control" v-model="board.board_title" placeholder="제목..." />
    </div>
    <div class="mb-3">
      <label for="board_content" class="form-label">내용 : </label>
      <textarea class="form-control" v-model="board.board_content" rows="10"></textarea>
    </div>
    <div class="col-auto text-center">
      <button type="submit" class="board-write-btn" v-if="type === 'regist'">글작성</button>
      <button type="submit" class="btn btn-outline-success mb-3" v-else>글수정</button>
      <button type="button" class="board-write-btn" @click="moveList">목록으로</button>
    </div>
  </form>
</template>

<style scoped>
.board-write-btn {
  border: 1px solid rgba(0, 0, 0, 0.4);
  background-color: white;
  margin-left: 10px;
  border-radius: 2px;
}

.board-write-btn:hover {
  background-color: rgba(0, 0, 0, 0.1);
}
</style>
