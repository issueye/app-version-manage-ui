<template>
	<div class="login-wrap">
		<div class="ms-login">
			<div class="ms-title">程序版本管理系统</div>
			<el-form :model="param" :rules="rules" ref="login" label-width="0px" class="ms-content">
				<el-form-item prop="username">
					<el-input v-model="param.username" placeholder="username">
						<template #prepend>
							<el-button :icon="User"></el-button>
						</template>
					</el-input>
				</el-form-item>
				<el-form-item prop="password">
					<el-input type="password" placeholder="password" v-model="param.password"
						@keyup.enter="submitForm(login)">
						<template #prepend>
							<el-button :icon="Lock"></el-button>
						</template>
					</el-input>
				</el-form-item>
				<div class="login-btn">
					<el-button type="primary" @click="submitForm(login)">登录</el-button>
					<el-button @click="handleCancel()">退出</el-button>
				</div>
			</el-form>
		</div>
	</div>
</template>

<script setup lang="ts">
import { ref, reactive } from 'vue';
import { useTagsStore } from '../store/tags';
import { useRouter } from 'vue-router';
import { ElMessage } from 'element-plus';
import type { FormInstance, FormRules } from 'element-plus';
import { Lock, User } from '@element-plus/icons-vue';
import { userStore, LInfo } from '../store/user';

interface LoginInfo {
	username: string;
	password: string;
}

const router = useRouter();
const param = reactive<LoginInfo>({
	username: 'admin',
	password: '123123'
});

const rules: FormRules = {
	username: [
		{
			required: true,
			message: '请输入用户名',
			trigger: 'blur'
		}
	],
	password: [{ required: true, message: '请输入密码', trigger: 'blur' }]
};
const login = ref<FormInstance>();
const user = userStore();
const submitForm = (formEl: FormInstance | undefined) => {
	if (!formEl) return;
	formEl.validate(async (valid: boolean) => {
		if (valid) {
			const info: LInfo = {
				account: param.username,
				password: param.password,
			};
			// 调用后台接口
			let isLogin = await user.login(info)
			// 如果登录成功
			if (isLogin) {
				ElMessage.success('登录成功');
				localStorage.setItem('ms_username', param.username);
				router.push('/');
			}
		} else {
			ElMessage.error('登录失败');
			return false;
		}
	});
};

// 退出程序
const handleCancel = () => {
	astilectron.sendMessage({ name: 'close-app' }, function (message: any) {
		console.log('回传消息', message);

	});
}


const tags = useTagsStore();
tags.clearTags();
</script>

<style scoped>
.login-wrap {
	/* position: relative; */
	width: 100%;
	height: 100%;
	background-image: url(../assets/img/login-bg.jpg);
	background-size: 100%;
}

.ms-title {
	width: 100%;
	line-height: 50px;
	text-align: center;
	font-size: 20px;
	color: #333300;
	border-bottom: 2px solid #ddd;
	/* 拖动 */
	-webkit-app-region: drag;
	-webkit-user-select: none;
}

.ms-login {
	position: absolute;
	left: 50%;
	top: 50%;
	width: 350px;
	margin: -190px 0 0 -175px;
	border-radius: 5px;
	background: rgba(69, 183, 135, 0.3);
	overflow: hidden;
}

.ms-content {
	padding: 30px 30px;
}

.login-btn {
	text-align: center;
}

.login-btn button {
	display: inline-flex;
	width: 40%;
	height: 36px;
	margin-bottom: 10px;
}
</style>
