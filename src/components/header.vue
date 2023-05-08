<template>
	<div class="header">
		<!-- 折叠按钮 -->
		<div class="collapse-btn" @click="collapseChage">
			<el-icon v-if="sidebar.collapse">
				<Expand />
			</el-icon>
			<el-icon v-else>
				<Fold />
			</el-icon>
		</div>
		<!-- <div :class="logoClass" @mousedown.native="handleMouseDown()" @mousemove.native="handleMouseMove()">程序版本管理系统</div> -->
		<div class="logo">程序版本管理系统</div>
		<div class="header-right">
			<div class="header-user-con">
				<!-- 消息中心 -->
				<!-- <div class="btn-bell" @click="router.push('/tabs')">
					<el-tooltip effect="dark" :content="message ? `有${message}条未读消息` : `消息中心`" placement="bottom">
						<i class="el-icon-lx-notice"></i>
					</el-tooltip>
					<span class="btn-bell-badge" v-if="message"></span>
				</div> -->
				<!-- 用户头像 -->
				<el-avatar class="user-avator" :size="25" :src="imgurl" />
				<!-- 用户名下拉菜单 -->
				<el-dropdown class="user-name" trigger="click" @command="handleCommand">
					<span class="el-dropdown-link">
						{{ username }} 
						<el-icon class="el-icon--right">
							<arrow-down />
						</el-icon>
					</span>
					<template #dropdown>
						<el-dropdown-menu>
							<el-dropdown-item command="user">个人中心</el-dropdown-item>
							<el-dropdown-item divided command="loginout">退出登录</el-dropdown-item>
						</el-dropdown-menu>
					</template>
				</el-dropdown>
				<svg-icon :name="fullScreen" @click="handleScreen()" class="svg-box"></svg-icon>
				<svg-icon :name="shutdown" @click="handleClose()" class="svg-box"></svg-icon>
			</div>
		</div>
	</div>
</template>
<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { useSidebarStore } from '../store/sidebar';
import { useRouter } from 'vue-router';
import imgurl from '../assets/img/img.jpg';
import SvgIcon from "./svgIcon.vue"
import { ElMessage, ElMessageBox } from 'element-plus';
import {Message, createMessage, MessageData } from '../utils/message';

const username: string | null = localStorage.getItem('ms_username');
const message: number = 2;

const sidebar = useSidebarStore();
// 侧边栏折叠
const collapseChage = () => {
	sidebar.handleCollapse();
};

const fullScreen = ref('full_screen');
const shutdown = ref('shutdown');
// const drag = ref('drag');
/*
float: left;
	width: 75%;
	line-height: 40px;
	-webkit-app-region: v-bind('drag');

*/ 
// const logoClass = ref("{float:left;line-height:40px;-webkit-app-region:drag;}");

onMounted(() => {
	if (document.body.clientWidth < 1500) {
		collapseChage();
	}
});

// // 鼠标移动窗体
// const handleMouseDown = () => {
// 	drag.value = 'drag';
// 	console.log('header drag down');
// 	logoClass.value = "{float:left;line-height:40px;-webkit-app-region:drag;}";
// }

// // 鼠标移动停止
// const handleMouseMove = () => {
// 	drag.value = 'null';
// 	console.log('header drag move'); 
// 	logoClass.value = "{float:left;line-height:40px;}";
// }

const isFullScreen = ref(false);

// 全屏或者恢复
const handleScreen = () => {
	// 如果对象存在则发送全屏
	isFullScreen.value = !isFullScreen.value;
	// astilectron.sendMessage({ name: 'screen' }, function (message: any) {
	// 	console.log('回传消息', message);
	// });

	Message.send(createMessage('screen'), (msg : MessageData) => {
		console.log('screen', msg.payload);
		
	})

	if (isFullScreen.value) {
		fullScreen.value = 'screen';
	} else {
		fullScreen.value = 'full_screen';
	}
}

// 关闭程序
const handleClose = () => {
	console.log('关闭程序');

	ElMessageBox.confirm('确定关闭程序吗？', '提示', {
		type: 'warning'
	})
		.then(() => {
			ElMessage.success('退出程序');
			// 如果对象存在则发送全屏
			astilectron.sendMessage({ name: 'close-app' }, function (message: any) {
				console.log('回传消息', message);

			});
		})
		.catch(() => { });
}

// 用户名下拉菜单选择事件
const router = useRouter();
const handleCommand = (command: string) => {
	if (command == 'loginout') {
		localStorage.removeItem('ms_username');
		router.push('/login');
	} else if (command == 'user') {
		router.push('/user');
	}
};
</script>
<style scoped>
.header {
	position: relative;
	box-sizing: border-box;
	width: 100%;
	height: 40px;
	font-size: 22px;
	color: #fff;
	background-color: #1781b5;
}

.collapse-btn {
	display: flex;
	justify-content: center;
	align-items: center;
	height: 100%;
	float: left;
	padding: 0 21px;
	cursor: pointer;
}

.svg-box {
	margin-left: 10px;
}

.svg-box:hover {
	width: 27px;
	height: 27px;
}

.header .logo {
	float: left;
	width: 75%;
	line-height: 40px;
	-webkit-app-region: drag;
}

.header-right {
	float: right;
	padding-right: 10px;
}

.header-user-con {
	display: flex;
	height: 40px;
	align-items: center;
}

.btn-fullscreen {
	transform: rotate(45deg);
	margin-right: 5px;
	font-size: 24px;
}

.btn-bell,
.btn-fullscreen {
	position: relative;
	width: 25px;
	height: 25px;
	text-align: center;
	border-radius: 15px;
	cursor: pointer;
	display: flex;
	align-items: center;
}

.btn-bell-badge {
	position: absolute;
	right: 4px;
	top: 0px;
	width: 8px;
	height: 8px;
	border-radius: 4px;
	background: #ed5a65;
	color: #fff;
}

.btn-bell .el-icon-lx-notice {
	color: #fff;
}

.user-name {
	margin-left: 10px;
}

.user-avator {
	margin-left: 20px;
}

.el-dropdown-link {
	color: #fff;
	cursor: pointer;
	display: flex;
	align-items: center;
}

.el-dropdown-menu__item {
	text-align: center;
}
</style>
