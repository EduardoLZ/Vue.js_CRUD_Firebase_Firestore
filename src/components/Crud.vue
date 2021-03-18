<template>
  <div class="container">
    <div class="row">
      <div class="col">
        <h2>Salvando el semestre - Youtube Channel</h2>
      </div>
    </div>
    <div class="row">
      <div class="col-4">
        <form>
          <div class="mb-3">
            <label class="form-label">Book</label>
            <input v-model="book" type="text" class="form-control" />
          </div>
          <button
            v-if="!updateState"
            @click="addBook"
            type="button"
            class="btn btn-primary"
          >
            Add Book
          </button>
          <button
            v-if="updateState"
            @click="updateBook2"
            type="button"
            class="btn btn-success"
          >
            Update Book
          </button>
        </form>
      </div>
      <div class="col-8">
        <div class="row">
          <div class="col-6 mt-2" v-for="book in books" :key="book">
            <div class="card">
              <div class="card-body">
                <h5 class="card-title">Title: {{ book.title }}</h5>
                <p class="card-text">ID: {{ book.id }}</p>
                <button
                  :disabled="updateState"
                  @click="updateBook(book)"
                  class="btn btn-success mr-2"
                >
                  Update
                </button>
                <button
                  :disabled="updateState"
                  @click="deleteBook(book.id)"
                  class="btn btn-danger"
                >
                  Delete
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import { db } from "../firebase";
import { onMounted } from "vue";

export default {
  name: "Crud",

  setup() {
    const books = ref([]);
    const book = ref("");
    const bookTemp = ref({});
    const updateState = ref(false);

    onMounted(() => {
      db.collection("books")
        .orderBy("date")
        .onSnapshot((querySnapshot) => {
          books.value = [];
          querySnapshot.forEach((doc) => {
            books.value.push(doc.data());
          });
        });
    });

    function addBook() {
      db.collection("books")
        .add({
          id: "",
          date: new Date().getTime(),
          title: book.value,
        })
        .then(function(docref) {
          console.log(docref.id);
          db.collection("books")
            .doc(docref.id)
            .update({ id: docref.id });
        });
      book.value = "";
    }
    function deleteBook(id) {
      db.collection("books")
        .doc(id)
        .delete();
    }
    function updateBook(bookTmp) {
      updateState.value = true;
      bookTemp.value = bookTmp;
      book.value = bookTmp.title;
    }
    function updateBook2() {
      let b = book.value;
      console.log(b);
      db.collection("books")
        .doc(bookTemp.value.id)
        .update({
          title: b,
        });
      updateState.value = false;
      book.value = "";
    }
    return {
      addBook,
      deleteBook,
      updateBook,
      updateBook2,
      updateState,
      book,
      bookTemp,
      books,
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
