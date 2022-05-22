<template>
  <div class="wrapper">

    <input type="text" @keyup="SearchUsers" id="searchUser" placeholder="Search For..." />
    <!-- <button class="deactivate-user" @click="SearchUsers">Search Users</button> -->
    <button class="deactivate-user" @click="DeActivateUsers">De Activate Users</button>

    <table id="crud">
      <caption>Users</caption>
      <thead>
        <tr>
          <td>id</td>
          <td>Name</td>
          <td>Phone</td>
          <td>Email</td>
          <td>User Status</td>
          <td>Actions</td>
        </tr>
      </thead>
      <tbody ref="tbody">
        <tr v-for="user in users" :key="user.id">
          <td>{{user.id}}</td>
          <td class="name">{{user.name}}</td>
          <td>{{user.phone}}</td>
          <td>{{user.email}}</td>
          <td>{{user.activeUser}}</td>
          <td>
            <button class="edit">Edit</button>
            <button class="delete">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
  export default {
    name: 'UsersList',

    data() {
      return {
        users: null,
        searchedUsers: null,
        list: null
      }
    },

    methods: {

      // fetching all users
      async fetchUsers() {
        let res = await fetch('http://localhost:5000/users');
        this.users = await res.json();
        this.list = this.$refs.tbody.children;
      },

      // search filter...
      SearchUsers(val) {
        // replace searchedUsers array empty to fetch users again for deactiation when we want...
        this.searchedUsers = new Array();
        let searchVal = val.target.value.toLowerCase();

        this.users.forEach((singleUser, i) => {
          let uname = singleUser.name.toLowerCase();

          if (uname.indexOf(searchVal) > -1) {
            // show filtered users
            this.list[i].classList.remove('d-none');
            // push filtered users for deactvation
            this.searchedUsers.push(singleUser);
          } 
          else this.list[i].classList.add('d-none');
        })

      },

      // deactivat users...
      DeActivateUsers() {

        this.searchedUsers.forEach(async u => {
          let updateTask = {
            ...u,
            activeUser: false
          }

          let request = await fetch('http://localhost:5000/users/' + u.id, {
            method: "put",
            headers: {
              'Content-Type': 'application/json'
            },
            body: JSON.stringify(updateTask)
          });

          const data = await request.json();
          console.log(data);

          // update the users array...
          this.users = this.users.map( task => { return task.id === u.id ? {...updateTask} : task;
          })
        })
      }

    },

    mounted() {
      this.fetchUsers();
    }
  }
</script>


<style scoped>
  * {
    padding: 0;
    margin: 0;
    box-sizing: border-box;
  }

  .d-none {
    display: none;
  }

  .d-block {
    display: block;
  }

  .wrapper {
    font-family: cursive;
  }

  #searchUser {
    padding: 10px 20px;
    border-radius: 5px;
    border: 1px solid #009688;
    margin-right: 10px;
    outline: 0;
  }

  #crud {
    width: 90%;
    margin: 10px auto;
    text-align: center;
  }

  #crud caption {
    background-color: #009688;
    color: #fff;
    padding: 10px 0;
    font-size: 1.5rem;
  }

  #crud thead {
    background-color: aliceblue;

  }

  #crud thead td {
    padding: 10px;
    color: #009688;
  }

  #crud tbody td {
    padding: 10px;
    border-bottom: 1px solid #009688;
    color: #666565;
  }

  #crud tbody td button,
  .deactivate-user {
    padding: 5px 7px;
    border: none;
    background: transparent;
    font-family: cursive;
    font-size: .9rem;
    color: #fff;
    border-radius: 3px;
    cursor: pointer;
  }

  #crud tbody td button.edit {
    background-color: #009688;
  }

  #crud tbody td button.delete {
    background-color: #e60909;
  }

  .deactivate-user {
    background-color: #009688;
    margin-top: 20px;
    padding: 10px 20px;
  }

  .popup {
    position: fixed;
    inset: 0;
    background-color: rgba(0, 0, 0, .4);
    display: none;
    place-items: center;
  }

  .popup.active {
    display: grid;
    animation: show .7s ease-in-out;
  }

  @keyframes show {
    0% {
      transform: translateY(-100%);
    }

    100% {
      transform: translateY(0);
    }
  }

  .popup form {
    background-color: #fff;
    display: flex;
    flex-direction: column;
    max-width: 350px;
    width: 100%;
    padding: 50px 20px;

  }

  .popup form input {
    width: 100%;
    background-color: transparent;
    border: none;
    box-shadow: inset 0 0 2px rgba(0, 0, 0, .4);
    margin: 15px 0;
    padding: 12px 15px;
    font-size: 1.1rem;
    font-family: cursive;

  }

  .popup form input[type="submit"] {
    background-color: #009688;
    color: #fff;
    cursor: pointer;
    max-width: 100px;
  }
</style>