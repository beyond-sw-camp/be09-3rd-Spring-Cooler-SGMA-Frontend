<template>
	<aside class="side-nav">
		<div class="group-name"><span>{{ groupData.group_name }}</span></div>
		<div class="menu-container">
			<SideBarMenu :to="`/study-groups/${groupData.group_id}/notices`">스터디 공지사항</SideBarMenu>
			<SideBarMenu :to="`/study-groups/${groupData.group_id}/schedules`">스터디 그룹 일정</SideBarMenu>
			<SideBarMenu :to="`/study-groups/${groupData.group_id}/boards`">스터디 자유게시판</SideBarMenu>
			<SideBarMenu :to="`/study-groups/${groupData.group_id}/members`">스터디 그룹원</SideBarMenu>
			<SideBarMenu :to="`/study-groups/${groupData.group_id}/recruitments`">스터디 모집글</SideBarMenu>
		</div>
	</aside>
</template>

<script setup>
import SideBarMenu from './SideBarMenu.vue';
import { reactive, onMounted } from 'vue';
import { useRoute } from 'vue-router';
import axios from 'axios';

const groupData = reactive({
	group_id: '',
	group_name: ''
});

const accessToken =
	localStorage.getItem('token') ? JSON.parse(localStorage.getItem('token')).accessToken : null;

const route = useRoute();

const fetchGroupData = async (groupId) => {
	try {
		const response = (await axios.get(`/study-group-service/api/study-groups/${groupId}`,
			{
				headers: {
					Authorization: `Bearer ${accessToken}`
				}
			})).data;
		if (response.success) {
			groupData.group_id = groupId;
			groupData.group_name = response.data.group_name;

			localStorage.setItem('groupData', JSON.stringify({
				group_id: groupId,
				group_name: response.data.group_name
			}));
		}
	} catch (error) {
		console.error(error);
	}
};

onMounted(() => {
	const storedData = JSON.parse(localStorage.getItem('groupData'));
	if (storedData && storedData.group_id === route.params.groupId) {
		groupData.group_id = storedData.group_id;
		groupData.group_name = storedData.group_name;
	} else {
		fetchGroupData(route.params.groupId);
	}
});
</script>

<style scoped>
.side-nav {
	position: fixed;
	left: 0px;
	bottom: 0px;
	display: flex;
	flex-direction: column;
	width: 42rem;
	height: calc(100% - 9rem);
	background-color: #ffffff;
	border-right: 1px solid #8c8c8c;
}

.group-name {
	display: flex;
	align-items: center;
	height: 12rem;
	width: 100%;
	color: #ffffff;
	background-color: #A1B872;
	font-size: 3.2rem;
	font-weight: 700;
}

.group-name span {
	padding-left: 3rem;
	/* 왼쪽 패딩 적용 */
}

.menu-container {
	display: flex;
	flex-direction: column;
	width: 100%;
	margin-top: 5rem;
}
</style>