<template>
    <div class="sidebar">
        <el-menu :default-active="onRoutes" class="el-menu-vertical-demo" theme="dark" unique-opened router>
            <template v-for="item in items">
                <template v-if="item.subs">
                    <el-submenu :index="item.index">
                        <template slot="title"><i :class="item.icon"></i>{{ item.title }}</template>
                        <el-menu-item v-for="(subItem,i) in item.subs" :key="i" :index="subItem.index">{{ subItem.title }}
                        </el-menu-item>
                    </el-submenu>
                </template>
                <template v-else>
                    <el-menu-item :index="item.index">
                        <i :class="item.icon"></i>{{ item.title }}
                    </el-menu-item>
                </template>
            </template>
        </el-menu>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                items: [
                    {
                        icon: 'el-icon-setting',
                        index: 'home',
                        title: '首页'
                    },
                    {
                        icon: 'el-icon-menu',
                        index: '1',
                        title: '系统监控',
                        subs: [
                            {
                                index: 'devManager',
                                title: '设备监控'
                            },
                            {
                                index: 'devAdd',
                                title: '性能监控'
                            },
                            {
                                index: 'devQuery',
                                title: '在线用户'
                            },
                            {
                                index: 'userBagHis',
                                title: '用户操作历史'
                            }
                        ]
                    },
                    {
                        icon: 'el-icon-menu',
                        index: '12',
                        title: '设备管理',
                        
                    },
                    {
                        icon: 'el-icon-menu',
                        index: '13',
                        title: '人员管理',
                        
                    },
                    {
                        icon: 'el-icon-menu',
                        index: '14',
                        title: '系统管理',
                        
                    },
                    
                ]
            }
        },
        computed:{
            onRoutes(){
                return this.$route.path.replace('/','');
            }
        }
    }
</script>

<style scoped>
    .sidebar{
        display: block;
        position: absolute;
        width: 250px;
        left: 0;
        top: 70px;
        bottom:0;
        background: #2E363F;
    }
    .sidebar > ul {
        height:100%;
    }
</style>
