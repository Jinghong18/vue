<template>
    <div class="header-container">
        <!-- 左边部分  tag按钮-->
        <div class="header-left flex-box">
            <el-icon class="icon" size="20" @click="store.toggleCollapse()">
                <Fold />
            </el-icon>
            <ul class="flex-box">
                <li v-for="(item, index) in selectMent" :key="item.path" class="flex-box tab" :class="{ active: route.path === item.path }">
                    <el-icon size="12">
                        <component :is="item.icon" />
                    </el-icon>
                    <router-link :to="item.path" class="flex-box tab-name">{{item.name}}</router-link>
                    <el-icon size="12" class="close"><Close @click="closeTab(item,index)" /></el-icon>
                </li>
            </ul>
        </div>
        <!-- 右边部分 下拉菜单-->
        <div class="header-right">
            <el-dropdown @command="handleClick">
                <div class="el-dropdown-link flex-box">
                    <el-avatar :src="userInfo.avatar" />
                    <p class="username">{{ userInfo.name }}</p>
                </div>
                <span class="el-dropdown-link">
                    Dropdown List
                    <el-icon class="el-icon--right">
                        <arrow-down />
                    </el-icon>
                </span>
                <template #dropdown>
                    <el-dropdown-menu>
                        <el-dropdown-item command="logout">退出</el-dropdown-item>
                    </el-dropdown-menu>
                </template>
            </el-dropdown>
        </div>
    </div>
</template>

<script setup>
import { Fold } from '@element-plus/icons-vue'
import { useMenuStore } from '../stores/index'
import { useRoute, useRouter } from 'vue-router'

const route = useRoute()
const router = useRouter()

const store = useMenuStore()
const selectMent = store.selectMenu

const userInfoString = localStorage.getItem("pz_userInfo");
const userInfo = userInfoString ? JSON.parse(userInfoString) : null;

const closeTab = (item, index) => {
    store.closeSeldetMenu(item)
    // 非当前页
    if (item.path !== route.path) {
        return
    }
    const selectMenuData = store.selectMenu
    // 当前页是最后一个
    if (index === selectMenuData.length) {
        // 只有一个tab
        if (!selectMenuData.length) {
            router.push('/')
        }else {
            router.push(selectMenuData[index - 1].path)
        }
    }else {
        // 中间的tab
        router.push(selectMenuData[index].path)
    }
}

const handleClick = (command) => {
    if (command === "logout") {
        localStorage.removeItem("pz_token");
        localStorage.removeItem("pz_userInfo");
        localStorage.removeItem("pz_v3pz");
        // 退出登录
        // 跳转到登录页面
        // router.push({ path: "/login" });
        window.location.href = window.location.origin;
    }
}
</script>

<style scoped lang="less">
.flex-box {
    display: flex;
    align-items: center;
    height: 100%;
}

.header-container {
    display: flex;
    justify-content: space-between;
    align-items: center;
    height: 100%;
    background-color: #fff;
    padding-right: 25px;

    .header-left {
        .icon {
            width: 45px;
            height: 100%;
            color: #493D9E;
        }

        .icon:hover {
            background-color: #f5f5f5;
            cursor: pointer;
        }

        .tab {
            padding: 0 10px;
            height: 100px;

            .tab-name {
                margin: 0 5px;
            }

            .close {
                visibility: hidden;
            }

            &.active {
                background-color: #f5f5f5;

                a {
                    color: #409eff;
                }

                i {
                    color: #409eff;
                }
            }
        }

        .tab:hover {
            background-color: #f5f5f5;
            .close {
                visibility: visible;
                cursor: pointer;
                color: #409eff;
            }
        }
    }

    .header-right {
        .username {
            margin-left: 10px;
            font-weight: bold
        }
    }
}

a {
    height: 100%;
    color: #333;
    font-size: 15px;
}
</style>