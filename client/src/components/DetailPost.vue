<template>
  <div>
    <div class="card mb-4" v-for="(post,i) in posts" :key="i" v-if="post._id == postid">
      <img class="card-img-top" v-bind:src="post.image" alt="Card image cap">
      <div class="card-body">
        <h2 class="card-title">{{ post.title }}</h2>
        <p class="card-text">{{ post.content }}</p>
        <hr>
        <p class="comment-user">Comments:</p>
        <hr>
        <div v-for="(comment,i) in post.comments" :key="i">
          <div class="comment-user">
            <p>User: {{ comment.user.email }}</p>
            <p>Comment: {{ comment.comment }}</p> 
            <hr>
          </div>
        </div>
        <div class="comment-user">
          <textarea rows="4" cols="27" v-model="newComment"></textarea> <br/>
          <button class="btn" @click="addComment(post._id)">Add Comment</button>
        </div>
      </div>
      <div class="card-footer text-muted">
        Posted on {{post.createdAt.slice(0, 10)}} by
        <a href="#">Zena Vilenia</a>
      </div>
    </div>
  </div>
</template>

<script>
import { mapState, mapActions } from 'vuex'

export default {
  name: 'Post',
  data () {
    return {
      category: this.$route.params.id,
      postid: this.$route.params.postid,
      newComment: ''
    }
  },
  methods: {
    addComment: function(postid) {
      let user = localStorage.getItem('token')
      if(!user) {
        alert('Login first')
      } else {
        axios.post('http://35.197.139.113/posts/addcomment/', {
          id: postid,
          comment: this.newComment
        }, {
          headers: {
            token: user
          }
        })
          .then(response => {
            console.log(response)
            this.newComment = ''
            this.$router.go()
          })
          .catch(err => {
            console.error(err)
          })
      }
      
    }
  },
  computed: {
    ...mapState([
      'posts'
    ]),
  },
  watch:{
    $route: function (to, from){
      this.$router.go()
    }
  } 
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.comment-user {
  font-weight: bold;
  text-align: left;
}
</style>
