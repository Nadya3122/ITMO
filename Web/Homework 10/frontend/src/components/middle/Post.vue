<template>
    <div>
        <Article :post="post"/>
        <h1>Comments</h1>
        <div style="padding-left: 2em">
            <article v-for="comment in comments" :comment="comment" :key="comment.id">
                <div class="information">By {{ comment.author.login }}</div>
                <div class="body">{{ comment.text }}</div>
            </article>
        </div>
        <div class="form-box">
            <div class="header">New comment</div>
            <div class="body">
                <form method="post" @submit.prevent="onAddComment">
                    <div class="field">
                        <div class="name">
                            <label for="text">Text</label>
                        </div>
                        <div class="value">
                            <textarea id="text" rows="10" name="text" v-model="text"></textarea>
                        </div>
                        <div class="error"><pre>{{ error }}</pre></div>
                    </div>
                    <div class="button-field">
                        <input type="submit" value="Add">
                    </div>
                </form>
            </div>
        </div>
    </div>
</template>

<script>
    import Article from "./Article";
    import axios from 'axios'

    export default {
        name: "Post",
        components: { Article },
        props: ['postId'],
        data: function() {
            return {
                post: {},
                comments: {},
                error: "",
                text: "",
            }
        },
        methods: {
            onAddComment() {
                this.$root.$emit("onAddComment", this.post.id, this.text);
                this.text = "";
            },
            load() {
                axios.get("post", {params: {id: this.postId}}).then(response => this.post = response.data);
                axios.get("comments", {params: {postId: this.postId}}).then(response => this.comments = response.data);
            }
        },
        watch: {
            postId: function () {
                this.load();
            }
        },
        beforeMount() {
            this.$root.$on("onAddCommentValidationError", error => this.error = error);
            this.$root.$on("onAddNewComment", comment => this.comments.push(comment));
            this.text = this.error = "";
        },
        mounted () {
            this.load();
        },
    }
</script>

<style scoped>

</style>