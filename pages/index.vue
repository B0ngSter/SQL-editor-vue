<template>
    <div id="app" class="customBG">
        <div class="flex flex-row">
          <div class="w-2/12 min-h-screen">
            <p class=" text-gray-500 text-center my-4">All Tables</p>
            <div v-for="(table, idx) in Object.keys(data)" :key="idx" class="tblBox p-5 m-1 cursor-pointer">
              <div class="flex justify-between" @click="openTable(table, idx)">
                <p class="text-white">{{ table }}</p>
                <span v-if="ext.includes(idx)" class="material-icons text-white">keyboard_arrow_up</span>
                <span v-else class="material-icons text-white">keyboard_arrow_down</span>
              </div>
              <div v-if="ext.includes(idx)" class="ml-4">
                <div v-for="(tableKeys, idy) in tableData" :key="idy" class="flex py-3 flex-row items-center" @click="tableData[idy].keyChecked = !tableData[idy].keyChecked">
                  <input type="checkbox" v-model="tableKeys.keyChecked">
                  <p class="px-3 text-gray-500">{{ tableKeys.keyValue }}</p>
                </div>
              </div>
            </div>
          </div>
          <div class="flex flex-col w-10/12">
            <div class="btnn flex justify-center">
              <button @click="execute">Execute</button>
            </div>
             <custom-codemirror
              class="w-full mt-2"
              v-model="sqlEditorList.sql"
              :sqlEditorEnabled="true"
              :key="sqlEditorList.id"
            />
            <PresentTheData class="w-full" v-if="dataToPresent.length" :dataToPresent="dataToPresent" />
            <p v-else class=" text-gray-500 mt-12 text-center">Please write a select query or click on any table and select its rows and execute to see the result</p>
          </div>
        </div>
    </div>
</template>

<script>
import CustomCodemirror from '~/components/custom-codemirror'
import PresentTheData from '~/components/present-the-data'
import * as d3 from 'd3'
export default {
    name: 'App',
    components: {
      CustomCodemirror,
      PresentTheData
    },
    methods: {
    },
    data() {
      return {
          sqlEditorList: {
            id: 0,
            sql: '',
          },
          tableData: [],
          ext: [],
          data: {
            customers: [],
            categories: [],
            employee_territories: [],
            employees: [],
            order_details: [],
            orders: [],
            products: [],
            regions: [],
            shippers: [],
            suppliers: [],
            territories: []
          },
          dataToPresent: []
      }
    },
    mounted () {
      const loadTime = window.performance.timing.domContentLoadedEventEnd- window.performance.timing.navigationStart
      console.log(loadTime)
      console.log(window.performance)
      const tables = ['customers.csv', 'categories.csv', 'employee_territories.csv', 'employees.csv', 'order_details.csv', 'orders.csv', 'products.csv', 'regions.csv', 'shippers.csv', 'suppliers.csv', 'territories.csv']
      tables.forEach((key) => {
        const TableName = key.slice(0, -4)
        d3.csv(key, (myData) => {
          this.data[TableName].push(myData)
        })
      })
    },
    methods: {
      openTable (tableName, id) {
        if (this.ext.includes(id)) {
          this.ext = []
        } else {
          this.tableData = []
          this.ext = []
          this.ext.push(id)
          Object.keys(this.data[tableName][0]).forEach((key) => {
            const customData = {
              keyChecked: false,
              keyValue: key
            }
            this.tableData.push(customData)
          })
        }
      },
      execute () {
        // backend is needed here to execute the sql on its full power
        this.dataToPresent = []
        if (this.tableData.length) {
          let selectKeys = ''
          this.tableData.forEach((key) => { // SELECT employeeID FROM employee_territories;
            if (key.keyChecked) {
              if (selectKeys.length) {
                selectKeys += ', ' + key.keyValue
              } else {
                selectKeys = key.keyValue
              }
            }
          })
          this.sqlEditorList.sql = `SELECT ${selectKeys} FROM  ${Object.keys(this.data)[this.ext[0]]};`
          const keywords = this.sqlEditorList.sql.split(" ") // SELECT employeeID FROM employee_territories;
          this.selectQuery(keywords)
          console.log(this.tableData)
          debugger
        } else {
          const query = { ...this.sqlEditorList }
          const numberOfLines = this.count(query.sql, '\n')
          let Lines = []
          for (let i = 0; i < numberOfLines + 1; i++) {
            const idxComma = query.sql.indexOf('\n')
            if (idxComma !== -1) {
              Lines.push(query.sql.slice(0, idxComma))
              query.sql = query.sql.slice(idxComma + 1)
            } else {
              Lines.push(query.sql)
            }
          }
          Lines.forEach((key) => {
            const keywords = key.split(" ") // SELECT employeeID FROM employee_territories;
            const prefix = keywords[0].toLowerCase()
            debugger
            prefix === 'select' ? this.selectQuery(keywords) : null
          })
        }
        // const newGroup = d3.group(this.data.categories, d => d.categoryName)
        // console.log(newGroup)
      },
      // updateQuery (keywords) {
      //   // UPDATE customers SET companyName = 'Alfred Schmidt', contactName = 'Frankfurt' WHERE customerID = 'ANTON';
      //   //    0       1      2        3     4         5               6     7      8        9      10      11   12
      //   const TableToUpdate = keywords[1]
      //   const endOfTheList = keywords.indexOf('WHERE') // 9
      //   const keysToUpdateList = []
      //   keywords.forEach((key) => {
      //     this.data[TableToUpdate].forEach((tableKey) => {
      //       if (key === tableKey) {
      //         !keysToUpdateList.includes(key) ? keysToUpdateList.push(key) : null
      //       }
      //     })
      //   })
      //   debugger
        

      // },
      selectQuery (keywords) {
        const fromKey = keywords.indexOf("FROM")
        let ListOfTableEl
        if (fromKey !== -1) {
          ListOfTableEl = keywords.slice(1, fromKey)
          ListOfTableEl.map((key, i) => {
            const indexOfComma = key.indexOf(',')
            if (indexOfComma !== -1) {
              if (indexOfComma === key.length - 1) {
                ListOfTableEl[i] = key.slice(0, -1)
                debugger
              } else if (indexOfComma === 0) {
                key = key.slice(1)
              } else if (indexOfComma !== key.length - 1 && indexOfComma !== 0) {
                const times = this.count(key, ',')
                let keys = []
                for (let i = 0; i < times + 1; i++) {
                  const idxComma = key.indexOf(',')
                  if (idxComma !== -1) {
                    keys.push(key.slice(0, idxComma))
                    key = key.slice(idxComma + 1)
                  } else {
                    keys.push(key)
                  }
                }
                ListOfTableEl = [...keys]
              }
            }
          })
        }
        let tableName = keywords[keywords.length - 1]
        if (tableName.includes(';')) {
          tableName = tableName.slice(0, -1).toLowerCase()
        }
        this.data[tableName].forEach((key) => {
          let sampleData = {}
          ListOfTableEl.forEach((el) => {
            sampleData[el] = key[el]
          })
          this.dataToPresent.push(sampleData)
        })
        debugger
      },
      count(str, find) {
        return (str.split(find)).length - 1
      }
    }
}
</script>

<style>
.tblBox {
  box-shadow: inset -2px -2px 4px 0 #262626, inset 1px 1px 2px 0 #999, -3px -3px 6px 0 #4D4D4D, 4px 4px 8px 0 #1F1F1F;
}
.btnn {
  height: 35px;
  width: 85px;
  background-color: red;
  color: white;
}
.customBG {
  background-color: #101418;
}
</style>
