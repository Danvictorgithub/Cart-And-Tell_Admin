<script setup>
const API = useRuntimeConfig().public.API;
const token = useCookie("token");
const path = useRoute().path;
const controller = new AbortController();
const signal = controller.signal;
const { data: users, pending: pending1 } = await useFetch(
  `${API}/user/count/?status=Removed`,
  {
    signal,
    lazy: true,
    headers: {
      Authorization: `Bearer ${token.value}`,
    },
    key: path + "1",
  }
);
const { data: banned, pending: pending2 } = await useFetch(
  `${API}/user/count/?status=Banned`,
  {
    signal,
    lazy: true,
    headers: {
      Authorization: `Bearer ${token.value}`,
    },
    key: path + "2",
  }
);
const { data: merchants, pending: pending3 } = await useFetch(
  `${API}/merchant/count?status=Removed`,
  {
    signal,
    lazy: true,
    headers: {
      Authorization: `Bearer ${token.value}`,
    },
    key: path + "3",
  }
);
onBeforeRouteLeave((to, from) => {
  if (pending1 || pending2 || pending3) {
    controller.abort();
  }
});
</script>

<template>
  <div class="bg-[#F8F9FD]">
    <div class="flex justify-between">
      <ArchivesSideBar />
      <div
        class="p-4 w-full min-h-[100svh] overflow-x-scroll overflow-y-hidden"
      >
        <div class="bg-white rounded-3xl w-full p-4 text-center">
          <h1 class="text-5xl font-black">Overview</h1>
        </div>
        <div class="bg-white rounded-3xl w-full p-4 text-center h-full mt-4">
          <div
            class="flex justify-between items-center w-full"
            v-if="pending1 && pending2 && pending3"
          >
            <div
              class="flex flex-col justify-center items-center shadow-sm p-4 rounded-md w-full h-[96px] animate-pulse"
              v-for="i in Array.from({ length: 3 })"
            >
              <div class="h-5 w-80 bg-gray-500 mb-4 rounded-md"></div>
              <div class="h-5 w-12 bg-gray-500 rounded-md"></div>
            </div>
          </div>
          <div class="flex justify-between items-center w-full" v-else>
            <div
              class="flex flex-col justify-center items-center shadow-sm p-4 rounded-md w-full"
            >
              <h1 class="text-2xl font-bold">Total Archived Users</h1>
              <h2 class="text-2xl font-bold">{{ users ? users : 0 }}</h2>
            </div>
            <div
              class="flex flex-col justify-center items-center shadow-sm p-4 rounded-md w-full"
            >
              <h1 class="text-2xl font-bold">Total Banned Users</h1>
              <h2 class="text-2xl font-bold">
                {{ banned ? banned : 0 }}
              </h2>
            </div>
            <div
              class="flex flex-col justify-center items-center shadow-sm p-4 rounded-md w-full"
            >
              <h1 class="text-2xl font-bold">Total Archived Merchants</h1>
              <h2 class="text-2xl font-bold">
                {{ merchants ? merchants : 0 }}
              </h2>
            </div>
          </div>
        </div>
      </div>
      <!-- <SettingBar/> -->
    </div>
  </div>
</template>

<style scoped></style>
