
<template>
  <el-container>
    <!-- <el-header>Admin Page</el-header> -->
    <el-main>
      <template>
        <el-tabs v-model="activeName" @tab-click="handleClick">
          <el-tab-pane label="주제 입력" name="topic">
            <div class="grid-content bg-purple">
              <el-form ref="form" :model="form" :rules="form_rules" label-width="120px">
                <el-form-item label="kor">
                  <el-input v-model="form.topic_kor"></el-input>
                </el-form-item>
                <el-form-item label="eng">
                  <el-input v-model="form.topic_eng"></el-input>
                </el-form-item>
                <el-form-item label="Type">
                  <el-select v-model="topic_types[form.type]" placeholder="please select type">
                    <el-option label="주력" value="0"></el-option>
                    <el-option label="비주력" value="1"></el-option>
                    <el-option label="Role Play" value="2"></el-option>
                    <el-option label="Advance" value="3"></el-option>
                  </el-select>
                </el-form-item>
                <el-form-item>
                  <el-button type="primary" @click="onSubmitTopic">Create</el-button>
                </el-form-item>
              </el-form>
            </div>
          </el-tab-pane>
          <el-tab-pane label="문제 입력" name="question">
            <div class="grid-content bg-purple-light">
              <el-form ref="question_form" :model="question_form" label-width="120px">
                <el-form-item label="Topic">
                  <el-select v-model="question_form.topic_id" placeholder="please select type">
                    <template v-for="t in question_form.topic">
                        <el-option v-bind:label="t.topic_kor" v-bind:value="t.topic_id"></el-option>
                    </template>
                  </el-select>
                </el-form-item>
                <el-form-item label="eng">
                  <el-input type="textarea" v-model="question_form.question_eng"></el-input>
                </el-form-item>
                <el-form-item label="kor">
                  <el-input v-model="question_form.question_kor" clearable></el-input>
                </el-form-item>
                <el-form-item>
                  <el-button type="primary" @click="onSubmitQuestion">Create</el-button>
                </el-form-item>
              </el-form>
            </div>
          </el-tab-pane>
          <el-tab-pane label="세트 생성" name="set">
            <div class="grid-content bg-purple">
              <el-form ref="set_form" :model="set_form" label-width="120px">
                <el-form-item label="Topic">
                  <el-select v-model="set_form.topic_id" @change="onChangeTopic" placeholder="please select type">
                    <template v-for="t in set_form.topic">
                        <el-option v-bind:label="t.topic_kor" v-bind:value="t.topic_id"></el-option>
                    </template>
                  </el-select>
                </el-form-item>
              </el-form>
              <template>
                <el-table ref="multipleTable" :data="set_form.questionData" style="width: 100%" @selection-change="handleSelectionChange">
                  <el-table-column type="selection" width="55"></el-table-column>
                  <el-table-column type="expand">
                    <template slot-scope="props">
                      <p>{{ props.row.question_eng }}</p>
                    </template>
                  </el-table-column>
                  <el-table-column property="question_id" label="#" width="50"></el-table-column>
                  <el-table-column property="topic_kor" label="topic_kor" width="150"></el-table-column>
                  <el-table-column property="topic_eng" label="topic_eng" width="150"></el-table-column>
                  <el-table-column label="type" width="100">
                    <template slot-scope="props">
                      <p>{{ topic_types[props.row.type] }}</p>
                    </template>
                  </el-table-column>
                  <el-table-column property="question_kor" label="question_kor" width="300"></el-table-column>
                  <el-table-column property="probability" label="P" width="60"></el-table-column>
                  <el-table-column label="Date" width="200">
                    <template slot-scope="scope">{{ scope.row.update_time }}</template>
                  </el-table-column>
                  <el-table-column label="Operations">
                    <template slot-scope="scope">
                      <el-button size="mini" type="primary" @click="handleGenerateMp3(scope.$index, scope.row)">Generate mp3</el-button>
                    </template>
                  </el-table-column>
                </el-table>
                <div style="margin-top: 20px">
                  <el-button @click="createSet">Create Set</el-button>
                </div>
              </template>
            </div>
          </el-tab-pane>
          <el-tab-pane label="세트 관리" name="manageSet">
            <el-select v-model="set_manage.topic_id" @change="onChangeTopicInManage" placeholder="please select type">
              <template v-for="t in set_manage.topic">
                  <el-option v-bind:label="t.topic_kor" v-bind:value="t.topic_id"></el-option>
              </template>
            </el-select>
            <template>
                <el-table ref="multipleTable2" :data="set_manage.questionSetData" style="width: 100%">
                  <el-table-column type="expand">
                    <template slot-scope="props">
                      <p>1. {{ props.row.question_id_1_eng }}</p>
                      <p>2. {{ props.row.question_id_2_eng }}</p>
                      <p>3. {{ props.row.question_id_3_eng }}</p>
                    </template>
                  </el-table-column>
                  <el-table-column property="question_id_1_kor" label="question_id_1" width="300"></el-table-column>
                  <el-table-column property="question_id_2_kor" label="question_id_2" width="300"></el-table-column>
                  <el-table-column property="question_id_3_kor" label="question_id_3" width="300"></el-table-column>
                  <el-table-column label="Date" width="200">
                    <template slot-scope="scope">{{ scope.row.update_time }}</template>
                  </el-table-column>
                  <el-table-column label="Operations">
                    <template slot-scope="scope">
                      <el-button size="mini" type="danger" @click="handleListen(scope.$index, scope.row)">Listen</el-button>
                    </template>
                  </el-table-column>
                </el-table>
              </template>
          </el-tab-pane>
        </el-tabs>
      </template>
    </el-main>
  </el-container>
</template>

<script>
import axios from 'axios';

var hash = require('object-hash');

axios.defaults.baseURL = 'http://192.168.137.75:3000/api';
axios.defaults.headers.post['Content-Type'] = 'application/json';
axios.defaults.headers.get['Content-Type'] = 'application/json';

export default {
  data() {
    return {
      activeName: 'topic',
      topic_types: ['주력', '비주력', 'Role Play', 'Advance'],
      topic: [],
      form: {
        topic_kor: '',
        topic_eng: '',
        type: 0
      },
      form_rules: {
        topic_kor : [
          { required: true, message: 'Please input topic', trigger: 'blur' },
          { min: 3, message: 'Length should be 3 to 5', trigger: 'blur' }
        ]
      },
      question_form: {
        topic_id: '',
        question_kor: '',
        question_eng: '',
        probability: 0.7
      },
      set_form: {
        topic_id: '',
        topic: [],
        questionData: [],
        multipleSelection: []
      },
      set_manage: {
        topic_id: '',
        topic: [],
        questionSetData: []
      }
    }
  },
  methods: {
    onSubmitTopic() {
      this.$refs['form'].validate((valid) => {
        if (valid) {
            axios.post('/topic/add', this.form)
              .then(response => {
                this.$message({
                  message: 'Success to add topic.',
                  type: 'success'
                });
              })
              .catch(e => {
                this.$message.error(e);
              })
          } else {
            console.log('error submit!!');
            return false;
          }
      });
    },
    onSubmitQuestion() {      
      let that = this;

      axios.post('/question/add', this.question_form)
        .then(response => {
          this.$message({
            message: 'Success to add question.',
            type: 'success'
          });

          that.question_form.question_kor = '';
          that.question_form.question_eng = '';
        })
        .catch(e => {
          this.$message.error(e);
        })
    },
    onChangeTopic(val) {
      let url = '/question/' + val;
      let that = this;

      axios.get(url)
        .then(response => {
          that.set_form.questionData = response.data.body.questions;
        })
        .catch(e => {
          this.errors.push(e)
        })
    },
    onChangeTopicInManage(val) {
      let url = '/set/' + val;
      let that = this;

      axios.get(url)
        .then(response => {
          that.set_manage.questionSetData = response.data.body.set;
        })
        .catch(e => {
          this.errors.push(e)
        })
    },
    createSet() {
      let questions_ids = [];

      this.set_form.multipleSelection.forEach(function(v) {
        questions_ids.push(v.question_id);
      });

      this.$prompt('Please input set name', 'Define Sets', {
        confirmButtonText: 'OK',
        cancelButtonText: 'Cancel'
      }).then(value => {
        axios.post('/set/add', { topic_id : this.set_form.topic_id, question_ids: questions_ids, description: value.value })
          .then(response => {
            this.$message({
              message: 'Success to add set.',
              type: 'success'
            });

            this.set_form.multipleSelection = [];
          })
          .catch(e => {
            this.$message.error(e);
          });
      }).catch(() => {
        this.$message({
          type: 'info',
          message: 'Input canceled'
        });       
      });
    },
    handleSelectionChange(val) {
      this.set_form.multipleSelection = val;
    },
    handleClick(tab, event) {
      console.log(tab, event);
    },
    handleListen(index, row) {
      let question_set_hash = hash([row.question_id_1, row.question_id_2, row.question_id_3]);
    },
    handleGenerateMp3(index, row) {
      console.log(index);
      console.log(row.question_id);
      let question_hash = hash(row.question_id);

      console.log(question_hash);

      axios.get(`/tts/generate/` + question_hash)
      .then(response => {
        console.log(resposne);
      })
      .catch(e => {

      })
    }
  },
  created() {
    let that = this;

    axios.get(`/topic`)
      .then(response => {
        that.topic = response.data.body.topics;
        that.question_form.topic = Array.from(that.topic);
        that.set_form.topic = Array.from(that.topic);
        that.set_manage.topic = Array.from(that.topic);
      })
      .catch(e => {
        this.errors.push(e)
      })
  }
}

/*
export default {
  data() {
    return {
      posts: [],
      errors: []
    }
  },

  // Fetches posts when the component is created.
  created() {
    axios.get(`http://jsonplaceholder.typicode.com/posts`)
    .then(response => {
      // JSON responses are automatically parsed.
      this.posts = response.data
    })
    .catch(e => {
      this.errors.push(e)
    })

    // async / await version (created() becomes async created())
    //
    // try {
    //   const response = await axios.get(`http://jsonplaceholder.typicode.com/posts`)
    //   this.posts = response.data
    // } catch (e) {
    //   this.errors.push(e)
    // }
  }
}
*/
</script>
