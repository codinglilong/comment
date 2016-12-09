<template>
    <div class="box">
        <div class="ui form segment">
            <div class="field">
                <label>请输入评论</label>
                <textarea v-model="content" @keyup.enter="addComment()"></textarea>
            </div>
            <div class="fluid ui red button" @click="addComment()">提交</div>
        </div>
        <div class="message">
            <div class="message-list" v-for="(item,index) in comments">
                <p>
                    {{item.content}}
                </p>
                <div class="message-info ui grid">
                    <div class="row">
                        <div class="eight wide column col-left">
                            {{item.created_at}}
                        </div>
                        <div class="eight wide column col-right">
                            <div class="ui label">
                                <a href="#" @click="addPraise(item._id)">
                                    <i class="like outline icon"></i>
                                    <div class="detail">{{item.praise}}</div>
                                </a>
                            </div>
                            <div class="ui label" @click="addCriticize(item._id)">
                                <a href="#">
                                    <i class="dislike outline icon"></i>
                                    <div class="detail">{{item.criticize}}</div>
                                </a>
                            </div>
                            <div class="ui label">
                                <a href="#" @click="isShowModal=true;delId=item._id">
                                    删除
                                    <i class="delete icon"></i>
                                </a>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="message-list no-data" v-show="comments.length===0">
                <p>
                    暂无数据
                </p>
            </div>
        </div>
        <div class="pageing" v-show="totalPage>1">
            <div class="ui borderless pagination menu grid">
                <a class="item" href="javascript:;" @click="pageMethod(1)" :class="{disabled:page===1}">
                    <i class="left arrow icon"></i>首页
                </a>
                <a class="item" href="javascript:;" @click="pageMethod(page-1)" :class="{disabled:page===1}">上一页</a>
                <a class="item" href="javascript:;" @click="pageMethod(page+1)" :class="{disabled:page===totalPage}">下一页</a>
                <a class="item" href="javascript:;" @click="pageMethod(totalPage)" :class="{disabled:page===totalPage}">
                    末页<i class="icon right arrow"></i>
                </a>
            </div>
        </div>
        <div class="modal" v-show="isShowModal">
            <div class="modal-content">
                <a href="javascript:;">
                    <i class="close icon" @click="isShowModal=false"></i>
                </a>
                <div class="header">删除</div>
                <div class="content">
                    <p>
                        您确认要删除此条留言嘛?
                    </p>
                </div>
                <div class="actions">
                    <div class="ui negative button" @click="isShowModal=false">
                        取消
                    </div>
                    <div class="ui positive right labeled icon button" @click="deleteComment(delId)">
                        删除
                        <i class="checkmark icon"></i>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
import If from "./InterfaceUrl";
export default {
    data() {
            return {
                content: '',
                comments: [],
                totalPage: 0,
                page: 1,
                delId:-1,
                isShowModal: false

            }
        },
        methods: {
            addComment() {
                this.$http.post(If.addComment, {
                    content: this.content
                }).then((res) => {
                    if (res.data.success === "yes") {
                        this.showComments();
                    }
                });
            },
            showComments() {
                if (this.page > 0) {
                    this.$http.get(If.showComments, {
                        params: {
                            page: this.page
                        }
                    }).then((res) => {
                        this.comments = res.data.comments;
                        this.totalPage = res.data.totalPage;
                        this.content = "";
                    }).catch(res => {});

                }
            },
            deleteComment(id) {
                this.$http.post(If.deleteComment, {
                    id: id
                }).then((res) => {
                    if (res.data.success === "yes") {
                        this.showComments();
                        this.isShowModal=false;
                    }
                }).catch(res => {});
            },
            addPraise(id) {
                this.$http.post(If.addPraise, {
                    id: id
                }).then((res) => {
                    if (res.data.success === "yes") {
                        this.showComments();
                    }
                });
            },
            addCriticize(id) {
                this.$http.post(If.addCriticize, {
                    id: id
                }).then((res) => {
                    if (res.data.success === "yes") {
                        this.showComments();
                    }
                });
            },
            pageMethod(page) {
                // console.log(page);
                if (page > 0 && page <= this.totalPage) {
                    this.page = page;
                }

            }
        },
        mounted() {
            this.showComments();
        },
        watch: {
            page() {
                this.showComments();
            }
        }
}
</script>
<style type="text/css">
.message {
    text-align: left;
    border: 1px solid #DEDEDE;
    padding: 1rem;
    box-shadow: 0 1px 2px 0 rgba(34, 36, 38, .15);
    border-radius: .28571429rem;
}

.message-list {
    border: 1px solid #DEDEDE;
    padding: 1rem;
    margin-top: 1rem;
    margin-bottom: 1rem;
    border-radius: .28571429rem;
}

.ui.label .detail {
    display: inline-block;
}

.col-left {
    line-height: 1;
    padding: .5833em .833em;
}

.col-right {
    text-align: right;
}

.pageing {
    text-align: left;
    margin-top: 20px;
}

.no-data {
    text-align: center;
}

.modal {
    position: fixed;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    background-color: rgba(0, 0, 0, .9);
    text-align: left;
}

.modal .modal-content {
    width: 50%;
    top: 50%;
    left: 50%;
    position: absolute;
    background-color: #fff;
    transform: translate(-50%, -50%);
    border-radius: 5px;
    position: relative;
}

.modal .modal-content .header {
    padding: 20px;
    background: linear-gradient(transparent, rgba(0, 0, 0, .05)) #fff;
    border-bottom: 1px solid #ccc;
    font-size: 1.6em;
    border-top-left-radius: 5px;
    border-top-right-radius: 5px;
}

.modal .modal-content .actions {
    padding: 10px 20px;
    background: #efefef;
    border-top: 1px solid #ccc;
    border-bottom-left-radius: 5px;
    border-bottom-right-radius: 5px;
    text-align: right;
}

.modal .modal-content .close.icon {
    position: absolute;
    right: 10px;
    top: 20px;
}

.modal .modal-content .content {
    padding: 2rem;
    font-size:1rem;
}
</style>
