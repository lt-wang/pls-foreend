<template>
	<!-- <el-container style="height: 100%; border: 1px solid #000000"> -->
	<el-container style="height: 100%;">
		<el-header style="font-size: 12px;">
			<span class="el-menu-item menu-text"
				style="font-size: 25px;font-weight: bolder;color:white;width:250px;">攀枝花物流管理平台</span>
			<!-- 			    <el-menu-item v-for="(item, index) in menuList" :key="index" style="width: 128px;" :index="item.path" :route="{path : item.path,query: {childrenMenus: JSON.stringify(item.children)}}"><i :class="item.icon"></i> {{item.title}}</el-menu-item> -->
			<!-- <el-menu-item index="/managerPage" :route="{path :'/managerPage'}"><i class="el-icon-s-tools"></i> 系统管理</el-menu-item> -->
			<el-popconfirm confirm-button-text='退出' cancel-button-text='点错了' icon="el-icon-info" icon-color="red"
				title="请问您是否要退出登录 ? " @confirm="loginOutUser()">
				<span class="el-menu-item menu-text menu-float" slot="reference">退出登录</span>
			</el-popconfirm>

			<img class="el-menu-item menu-text menu-img"
				src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fp0.itc.cn%2Fq_70%2Fimages03%2F20210626%2F71f657c7c43242a684f22c3ea6b858c8.gif&refer=http%3A%2F%2Fp0.itc.cn&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1658753581&t=00bc4b5c7ae62191b0ffe16863280f25" />
			<span class="el-menu-item menu-text menu-float">欢迎您{{userName}}, 您是{{authorityName}}</span>
		</el-header>

		<el-container style="height: 100%	; border: 1px solid #eee">
			<el-aside width="250px" style="background-color: rgb(237, 236, 246) ">
				<el-menu router :default-active="this.$router.path" style="height:500px">
					<el-menu-item index="mainPage"><i class="el-icon-message"></i>首页</el-menu-item>
					<el-submenu index="1">
						<template slot="title"><i class="el-icon-message"></i>基本信息管理</template>

						<el-menu-item index="employee">人员信息管理</el-menu-item>
						<el-menu-item index="car">车辆信息管理</el-menu-item>
						<el-menu-item index="address">地址基本信息管理</el-menu-item>
						<el-menu-item index="company">公司信息管理</el-menu-item>
						<el-menu-item index="material">物资管理</el-menu-item>
						<el-menu-item index="certification">证件信息管理</el-menu-item>
						<el-menu-item index="goods">货物信息管理</el-menu-item>
						<el-menu-item index="build">建筑信息管理</el-menu-item>
						<el-menu-item index="equipment">机电设备管理</el-menu-item>
					</el-submenu>
					<el-submenu index="2">
						<template slot="title"><i class="el-icon-menu"></i>购销存管理</template>
						
						<el-menu-item index="carManage">汽车购销</el-menu-item>
						<el-menu-item index="goodsManage">货物购销</el-menu-item>
					</el-submenu>
					<el-submenu index="3">
						<template slot="title"><i class="el-icon-setting"></i>汽车修检记录</template>
						<el-menu-item index="carFixLog">车辆维修记录</el-menu-item>
					</el-submenu>
          <el-submenu index="4">
            <template slot="title"><i class="el-icon-truck"></i>运输调度</template>
            <el-menu-item index="carOperation">车辆运输记录</el-menu-item>
          </el-submenu>
          <el-submenu index="5">
            <template slot="title"><i class="el-icon-view"></i>对外合作</template>
            <el-menu-item index="communication">信息交流</el-menu-item>
            <el-menu-item index="advertisement">广告信息交流</el-menu-item>
          </el-submenu>

				</el-menu>
			</el-aside>

			<el-main>
				<div>
					<el-breadcrumb class="app-breadcrumb" separator=">">
						<transition-group name="breadcrumb">
							<el-breadcrumb-item v-for="item in levelList" :key="item.path" :to="{ path: item.path }">
								<span>{{ item.meta.title }}</span>
							</el-breadcrumb-item>
						</transition-group>
					</el-breadcrumb>
				</div>

				<!-- <el-table :data="tableData">
					<el-table-column prop="date" label="日期" width="140">
					</el-table-column>
					<el-table-column prop="name" label="姓名" width="120">
					</el-table-column>
					<el-table-column prop="address" label="地址">
					</el-table-column>
				</el-table> -->
				<router-view>
					<router-view></router-view>
				</router-view>
			</el-main>
		</el-container>
	</el-container>

</template>

<script>
	import {
		loginOut
	} from '../api/getData.js';
	import {
		setStorage,
		getStorage
	} from "../utils/localStorage.js";
	export default {
		data() {
			const item = {
				date: '2016-05-02',
				name: '王小虎',
				address: '上海市普陀区金沙江路 1518 弄'
			};
			return {
				levelList: null, //面包屑
				userAccount: "", //用户账号
				userName:"",
				authorityName: "", //用户类型
				menuList: [], //用户菜单
				tableData: Array(20).fill(item) //表格数据
			}
		},
		watch: {
			$route() {
				this.getBreadcrumb()
			}
		},
		created() {
			this.getBreadcrumb()
		},
		methods: {
			getBreadcrumb() {
				let matched = this.$route.matched.filter(item => item.meta && item.meta.title)
				const first = matched[0]
				if (!this.isIndex(first)) {
					matched = [].concat(matched)
					this.levelList = matched
				} else {
					this.levelList = [{
						path: '/',
						meta: {
							title: '首页'
						}
					}]
				}
			},
			isIndex(route) {
				const redirect = route && route.redirect
				if (!redirect) {
					return false
				}
				return redirect === '/'
			},
			handleSelect(index, indexPath) {
				console.log(index)
				console.log(indexPath)
			},
			//退出登录
			loginOutUser() {
				loginOut().then(()=>{
					this.$router.push({name: "login"})
				});
				this.$message.warning("退出登录");
			},
			//初始化页面路由
			initRouter() {
				let userInfo = JSON.parse(getStorage("userInfo"));
				this.userName = userInfo.userName; //账号
				this.authorityName = userInfo.authorityName; //类型
			}
		},
		mounted() {
			this.$nextTick(() => {
				//跳转默认选中
				this.activeIndex = this.$route.path;
				// console.log(this.$router.path+"path");
				this.initRouter();//初始化页面路由
			})
		},

	}
</script>

<style>
	.menu-text {
		color: white !important;
	}

	.menu-float {
		float: right !important;
	}

	.menu-text:hover {
		background-color: rgb(67, 74, 80) !important;
	}

	.menu-text:focus {
		background-color: rgb(84, 92, 100) !important;
	}

	.menu-img {
		padding: 5px !important;
		border-radius: 15px;
		float: right !important;
	}

	.el-header {
		background-color: #B3C0D1;
		color: #333;
		line-height: 60px;
	}

	.el-aside {
		color: #333;
	}
</style>
