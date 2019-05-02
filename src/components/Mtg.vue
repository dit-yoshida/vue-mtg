<template>
  <div class="app">
    <!-- タイトル -->
    <div class="title">
      <h1>Markdown Table Generator</h1>
    </div>
    <!-- 操作ボタン -->
    <div class="controlButtons">
      <button v-on:click="addRow">Add Row</button>
      <button v-on:click="addColumn">Add Column</button>
      <button v-on:click="reset">Reset</button>
    </div>
    <!-- テーブルのヘッド部 -->
    <table v-if="tableHeadData.length > 0" class="tableHeader">
      <thead>
        <tr>
          <th></th>
          <th v-for="(col, index) in tableHeadData" v-bind:key="index">
            <button v-on:click="removeColumn(index)">Remove Column</button>
          </th>
          <th></th>
        </tr>
        <tr>
          <th></th>
          <th v-for="(col, index) in tableHeadData" v-bind:key="index">
            <button>←</button>
            <button>→</button>
          </th>
          <th></th>
        </tr>
        <tr>
          <th>
            <button>↑</button>
            <button>↓</button>
          </th>
          <th v-for="(col, index) in tableHeadData" v-bind:key="index">
            <input v-model="tableHeadData[index]" placeholder="Input text" />
          </th>
          <th>
            <button v-on:click="removeRow(0)">Remove Row</button>
          </th>
        </tr>
      </thead>
    </table>
    <!-- テーブルのボディ部 -->
    <table v-if="tableBodyDatas.length > 0" class="tableBody">
      <tbody>
        <tr v-for="(row, index) in tableBodyDatas" v-bind:key="index">
          <th>
            <button>↑</button>
            <button>↓</button>
          </th>
          <th v-bind:value="row" v-for="(col, colIndex) in row" v-bind:key="colIndex">
            <input v-model="row[colIndex]" placeholder="Input text" />
          </th>
          <th>
            <button v-on:click="removeRow(index + 1)">Remove Row</button>
          </th>
        </tr>
      </tbody>
    </table>
    <!-- Markdown 形式のデータ出力 -->
    <div class="mdResult">
      <textarea v-model="formattedTableDatas" cols="100" rows="10" class="mdResultTextbox">
      </textarea>
    </div>
    <!-- 出力ボタン -->
    <div class="exportButtons">
      <button v-on:click="copyToClipboard">
        Copy to Clipboard
      </button>
    </div>
  </div>
</template>

<script>
export default {
  data: () => {
    return {
      tableDatas: [
      ["", ""],
      ["", ""]
    ]
    }
  },
  methods: {
    addRow: function() {
      const newRow = this.tableDatas.length > 0 ? [...this.tableDatas[0].map(_ => "")] : [""]
      this.tableDatas = [...this.tableDatas, newRow]
    },
    addColumn: function() {
      if (this.tableDatas.length > 0) {
        this.tableDatas = this.tableDatas.map(row => [...row, ""])
      } else {
        this.tableDatas = [[""]]
      }
    },
    reset: function() {
      this.tableDatas = [
        [ "", "" ],
        [ "", "" ]
      ]
    },
    removeRow: function(rowIndex) {
      this.tableDatas = this.tableDatas.filter((_, index) => index !== rowIndex)
    },
    removeColumn: function(columnIndex) {
      const newTableDatas = this.tableDatas.map((row) => row.filter((_, index) => index !== columnIndex))
      // 列がなくなった場合は空配列にする
      this.tableDatas = newTableDatas[0].length > 0 ? newTableDatas : []
    },
    copyToClipboard: function() {
      this.$copyText(this.formattedTableDatas).then(() => {}, () => {})
    }
  },
  computed: {
    // ヘッド部に表示するデータ
    tableHeadData() {
      // 空の場合は空配列で返す
      if (this.tableDatas.length === 0) return []
      return this.tableDatas[0]
    },
    // ボディ部に表示するデータ
    tableBodyDatas() {
      return this.tableDatas.slice(1)
    },
    // テキストボックスに表示するテキストデータ生成
    formattedTableDatas() {
      return this.tableDatas.map((row, index) => {
        const colText = `| ${row.map(col => col).join(" | ")} |`
        // 2行目以降はデータ部のみ
        if (index > 0) return colText
        // 1行目はヘッドなのでセパレータも返す
        const separator = `| ${row.map(_ => "---").join(" | ")} |`
        return `${colText}\n${separator}`
      }).join("\n")
    }
  }
}
</script>

<style scoped>
</style>
