<template>
    <div>
      <Table :columns="historyColumns" :data="historyData"></Table>
      <Page :total="dataCount" :page-size="pageSize" show-total class="paging" @on-change="changepage" @on-page-size-change="pages" show-sizer show-elevator show-total></Page>
    </div>
</template>
<style scoped>
    .paging {
        float: left;
        margin-top: 10px;
    }
</style>
<script>
    import Cookies from 'js-cookie';

    export default {
        data() {
            return {
                ajaxHistoryData: [],
                // 初始化信息总条数
                dataCount: 0,
                // 每页显示多少条
                pageSize: 10,
                xia: 0, //下一页或者上一页的第一项索引值
                historyColumns: [{
                    "type": "selection",
                    "align": "center",
                    "width": "30",
                    "className": "border-r"
                }, {
                    "title": "用户名",
                    "align": "center",
                    "key": "username"
                }, {
                    "title": "姓名",
                    "align": "center",
                    "key": "name"
                }, {
                    "title": "所属组织机构",
                    "align": "center",
                    "key": "deptName"
                }, {
                    "title": "状态",
                    "align": "center",
                    "key": "status"
                }, {
                    "title": "联系电话",
                    "align": "center",
                    "key": "mobile"

                }, {
                    'title': '操作',
                    'align': 'center',
                    'key': 'action',
                    render: (h, params) => {
                        return h('div', [
                            h('Icon', {
                                props: {
                                    type: 'ios-baseball',
                                    size: 16
                                },
                                style: {
                                    marginLeft: '20px'
                                }


                            })

                        ])

                    }
                }],
                historyData: []
            }
        },
        methods: {
            // 获取历史记录信息
            handleListApproveHistory() {
                let sessionId = Cookies.get('status');
                let this1 = this;
                this.$http({
                        headers: {
                            "Authorization": sessionId,
                        },
                        method: 'post',
                        url: this1.GLOBAL.BASE_URL + '/sys/user/list',
                        params: {
                            'deptId': '001',
                            'offset': 0, //第一页第一项的索引
                            'limit': 10, //每页显示的条数
                        },
                    })
                    .then(function(res) {
                        debugger
                        this1.ajaxHistoryData = res.data.data;
                        this1.dataCount = res.data.total;

                        this1.historyData = this1.ajaxHistoryData;
                    })
                    .catch(function(error) {
                        //
                    })

            },
            pages(num) { //修改每页显示条数时调用
                debugger
                this.pageSize = num;
                this.changepage(1);
            },
            changepage(index) {
                //index当前页码
                this.xia = (index - 1) * this.pageSize;


                let sessionId = Cookies.get('status');
                let this1 = this;
                this.$http({
                        headers: {
                            "Authorization": sessionId,
                        },
                        method: 'post',
                        url: this1.GLOBAL.BASE_URL + '/sys/user/list',
                        params: {
                            'deptId': '001',
                            'offset': this1.xia,
                            'limit': this1.pageSize,
                        },
                    })
                    .then(function(res) {
                        debugger
                        this1.historyData = res.data.data;
                    })
                    .catch(function(error) {
                        //
                    })
            }
        },
        created() {
            this.handleListApproveHistory();
        }
    }
</script>