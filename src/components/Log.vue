<template>
  <div class="log">
    <h1>
      <el-button size="small" @click="changeExpandAll()">{{expandAll}}</el-button>
    </h1>
    <el-form :inline="true" :model="form">
      <el-row>
        <el-col :xs="4" :sm="3" :md="3" :lg="2">
          <el-form-item label="フィルター"></el-form-item>
        </el-col>
        <el-col :xs="20" :sm="21" :md="21" :lg="22">
          <el-form-item label="日時">
            <el-input v-model="form.from" size="small" placeholder="YYYY/MM/DD HH:MM:SS"></el-input>
          </el-form-item>
          <el-form-item label="〜">
            <el-input v-model="form.to" size="small" placeholder="YYYY/MM/DD HH:MM:SS"></el-input>
          </el-form-item>
          <el-form-item label="回答ID">
            <el-input v-model="form.user" size="small" placeholder="回答者"></el-input>
          </el-form-item>
          <el-form-item label="評価">
            <el-select v-model="selected" size="small">
              <el-option v-for="item in options" :key="item.value" :label="item.label" :value="item.value">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="キーワード">
            <el-input v-model="form.keyword" size="small" placeholder="キーワード"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" size="small" icon="search">適用</el-button>
            <el-button size="small">クリア</el-button>
          </el-form-item>
        </el-col>
      </el-row>
    </el-form>

    <el-table
      :data="log"
      stripe
      :default-expand-all="expandAll"
      style="width: 100%">
      <el-table-column type="expand">
        <template scope="props">
          <el-row>
            <el-col :span="2">
              <p>照会ルート</p>
            </el-col>
            <el-col :span="22">
              <div v-for="(item, index) in props.row.route" style="margin: 10px">
                <el-tag type="primary" style="width: 100px">Lv.{{index + 1}} {{item.name}}</el-tag>
                <el-tooltip :content="item.message" placement="top" v-for="item in item.answer" :key="item.class_name">
                  <el-tag type="gray" style="margin: 0 5px">{{item.class_name}} ({{item.confidence}})</el-tag>
                </el-tooltip>
                <el-button size="small" type="text" class="el-icon-d-arrow-right" style="margin: 0 10px;"> 学習データ登録
                </el-button>
              </div>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="2">
              <p>回答</p>
            </el-col>
            <el-col :span="22">
              <p>
                <el-button type="text" size="mini" :class="detail ? 'el-icon-caret-bottom' : 'el-icon-caret-right'"
                           @click='detail=!detail'></el-button>
                {{props.row.route[2].answer[0].class_name + ' (' + props.row.route[2].answer[0].confidence + ') ' + props.row.route[2].answer[0].message}}
                <span v-show="detail" v-for="(item, index) in props.row.route[2].answer" style="margin:0 16px">
              {{index === 0 ? '' : item.class_name + ' (' + item.confidence + ') ' + item.message}}<br>
            </span>
              </p>
            </el-col>
          </el-row>
        </template>
      </el-table-column>
      <el-table-column prop="datetime" label="日時" width="180"></el-table-column>
      <el-table-column prop="id" label="ID"></el-table-column>
      <el-table-column width="180" label="評価">
        <template scope="scope">
          <el-rate v-model="scope.row.score"
                   disabled
                   :colors="['#99A9BF', '#F7BA2A', '#FF9900']">
          </el-rate>
        </template>
      </el-table-column>
      <el-table-column prop="comment" label="コメント"></el-table-column>
      <el-table-column prop="question" label="問合せ内容"></el-table-column>
      <el-table-column prop="final_answer" label="最終回答"></el-table-column>
    </el-table>
  </div>
</template>

<script>
  import axios from 'axios'

  export default {
    name: 'log',
    data () {
      return {
        form: {},
        options: [
          {
            value: '全て',
            label: '全て'
          }, {
            value: '良い',
            label: '良い'
          }, {
            value: '悪い',
            label: '悪い'
          }
        ],
        selected: '全て',
        radio1: '人事 (0.87)',
        radio2: '就業規則 (0.99)',
        radio3: 'A01001 (0.87)',
        detail: false,
        expandAll: null,
        i: 0,
        log: null
      }
    },
    created () {
      this.init()
      this.expandAll = this.isExpandAll()
    },
    methods: {
      init () {
        axios.get('https://b20-o970605-json.mybluemix.net/log')
          .then((response) => {
            this.log = response.data
          })
      },
      isExpandAll () {
        const value = sessionStorage.getItem('expand_all')
        return value === 'true'
      },
      changeExpandAll () {
        this.expandAll = !this.expandAll
        sessionStorage.setItem('expand_all', this.expandAll)
        location.reload(false)
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
