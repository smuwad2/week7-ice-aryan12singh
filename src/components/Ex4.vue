<script>
import axios from 'axios';

export default {
  data() {
    return {
      posts: [],
      editingPostId: null,
      editForm: {
        entry: '',
        mood: ''
      },
      moods: ['Happy', 'Sad', 'Angry']
    }
  },
  
  computed: {
    // Automatically detects if running on localhost or Codespaces
    baseUrl() {
      if (window.location.hostname == 'localhost')
        return 'http://localhost:3000'
      else {
        const codespace_host = window.location.hostname.replace('5173', '3000')
        return `https://${codespace_host}`;
      }
    }
  },
  
  created() {
    this.fetchPosts();
  },
  
  methods: {
    fetchPosts() {
      // Use /posts endpoint (not /getPosts)
      axios.get(`${this.baseUrl}/posts`)
        .then(response => {
          this.posts = response.data;
          console.log('Posts loaded:', this.posts);
        })
        .catch(error => {
          console.error('Error fetching posts:', error);
        });
    },
    
    startEdit(post) {
      console.log('Edit clicked for post:', post);
      this.editingPostId = post.id;
      this.editForm.entry = post.entry;
      this.editForm.mood = post.mood;
    },
    
    cancelEdit() {
      this.editingPostId = null;
      this.editForm.entry = '';
      this.editForm.mood = '';
    },
    
    updatePost(postId) {
      axios.put(`${this.baseUrl}/updatePost`, {
        id: postId,
        entry: this.editForm.entry,
        mood: this.editForm.mood
      })
      .then(response => {
        console.log('Post updated:', response.data);
        
        const post = this.posts.find(p => p.id == postId);
        if (post) {
          post.entry = this.editForm.entry;
          post.mood = this.editForm.mood;
        }
        
        this.cancelEdit();
      })
      .catch(error => {
        console.error('Error updating post:', error);
      });
    }
  }
}
</script>

<template>
  <div>
    <h2>Blog Posts</h2>
    
    <table border="1" style="width: 100%; border-collapse: collapse;">
      <thead>
        <tr>
          <th>ID</th>
          <th>Entry</th>
          <th>Mood</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="post in posts" :key="post.id">
          <td>{{ post.id }}</td>
          <td>{{ post.entry }}</td>
          <td>{{ post.mood }}</td>
          <td>
            <button @click="startEdit(post)">Edit</button>
          </td>
        </tr>
      </tbody>
    </table>
    
    <div v-if="editingPostId !== null" id="editPost" style="margin-top: 20px; padding: 20px; border: 2px solid #ccc;">
      <h3>Edit Post</h3>
      
      <label>Entry</label><br>
      <textarea id="entry" v-model="editForm.entry" rows="5" cols="80"></textarea>
      <br><br>
      
      <label>Mood</label><br>
      <select id="mood" v-model="editForm.mood">
        <option v-for="m in moods" :key="m">{{ m }}</option>
      </select>
      <br><br>
      
      <button @click="updatePost(editingPostId)">Update Post</button>
      <button @click="cancelEdit">Cancel</button>
    </div>
  </div>
</template>

<style scoped>
table {
  margin-top: 20px;
}

th, td {
  padding: 10px;
  text-align: left;
}

th {
  background-color: #f0f0f0;
}

button {
  margin-right: 5px;
  padding: 5px 10px;
  cursor: pointer;
}
</style>