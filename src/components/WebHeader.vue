<template>
  <nav class="hidden lg:flex justify-between items-center px-8 py-2 bg-white shadow-lg fixed top-0 left-0 w-full z-50 ">
    <div><img src="/images/logo.png" alt="Logo" @click="goHome" class="w-28 mt-2 cursor-pointer" /></div>
    <div>
      <ul class="flex space-x-10 text-base">
        <li><button @click="goAsset" class="hover:text-blue-500" :class="{ 'text-blue-500 font-bold': isAssetActive }">내 자산분석</button></li>
        <li><button @click="goCommunity" class="hover:text-blue-500" :class="{ 'text-blue-500 font-bold': isCommunityActive }">커뮤니티</button></li>
        <li><button @click="goMypage" class="hover:text-blue-500" :class="{ 'text-blue-500 font-bold': isMypageActive }">마이페이지</button></li>
        <li><button @click="logout" class="hover:text-blue-500">로그아웃</button></li>
      </ul>
    </div>
  </nav>
</template>

<script setup>
import { useRouter, useRoute } from 'vue-router';
import { computed, onMounted, ref } from 'vue';
import HomeApi from "@/api/HomeApi";
import { useCommunityStore } from "@/stores/community";
const communityStore = useCommunityStore();

const router = useRouter();
const route = useRoute();


// mount 할 때 정보 가져오기
onMounted(() => {
    getUser();
    getAsset();
});

// 사용자 정보를 가져오는 함수
const userName = ref('');
const getUser = async () => {
    try {
        const userInfo = await HomeApi.getMyInfoFooter(); // /home/info 호출
        userName.value = userInfo.userName;
    } catch (error) {
       // console.error("사용자 정보 가져오는 함수 API 호출 중 오류 발생:", error);
    }
};

const assetFlag = ref(false);
const getAsset = async () => {
    try {
        const response = await HomeApi.accountListFooter();
        assetFlag.value = true;
    } catch (error) {
        assetFlag.value = false;
    }
};


//라우터 이동 함수
const goHome = () => {
  router.push({ name: 'main' });
};

const goAsset = () => {
    if(userName.value == 'NoLogin'){
        alert('로그인 후 이용해주세요.');
    }
    else if(assetFlag.value == false){
        alert('계좌 연결 후 이용해주세요.');
    }
    else{
        router.push({ name: 'asset' });
    }
};

const goCommunity = () => {
    communityStore.setCurrentPage(1);
    router.push({ name: 'communityList' });
};
const goMypage = () => {
    if(userName.value == 'NoLogin'){
        alert('로그인 후 이용해주세요.');
    }
    else{
        router.push({ name: 'mypage' });
    }
};

// 로그아웃 함수 추가
const logout = async () => {
  try {
    const response = refreshToken.logouting();
  } catch (error) {
    // console.log(error);
  }
  router.push('/');
};

// 메뉴 활성화 상태를 체크
const isAssetActive = computed(() => route.path.startsWith('/asset'));
const isCommunityActive = computed(() => route.path.startsWith('/community'));
const isMypageActive = computed(() => route.path === '/mypage');
</script>
