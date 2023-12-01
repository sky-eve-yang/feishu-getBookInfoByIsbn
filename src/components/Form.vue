

<template>
  <el-form ref="form" :model="formData" label-width="60px">

    <!-- 3. 接口地址 -->
    <el-form-item label="Api" size="large" required>
      address：<el-input v-model="interfaceAddr" placeholder="填写 api 请求地址" />
      appcode：<el-input type="password" v-model="appcode" placeholder="填写 appcode" />
      <el-link type="primary" href="https://jfsq6znqku.feishu.cn/docx/QiEbd3LIgoMfDDxKHMwcNyW4n4M?from=from_copylink" target="_blank">API 配置说明文档</el-link>
    </el-form-item>

    <!-- 2. 选择 isbn 字段 -->
    <el-form-item label="ISBN" size="large" required>
      <el-select v-model="IsbnFieldId" placeholder="请选择 ISBN 号对应字段" style="width: 100%">
        <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
      </el-select>
    </el-form-item>

  </el-form>

  <!-- 3.5 选择数据写入的方式：1-字段映射  2-创建字段 -->
  <!-- 4. 写回多维数据表格 -->
  <div class="write-data-tip">Select the data write back mode ↓</div>
  <el-radio-group v-model="dataWritenType" size="default">
    <el-radio-button label="Only Creation" />
    <el-radio-button label="Only Mapping" />
    <el-radio-button label="Mixed Mode" />
  </el-radio-group>

  <!-- Only Creation: -->
  <div v-if="dataWritenType == 'Only Creation'" class="field-creation">
    <el-alert title="请勾选需要创建的字段" type="info" show-icon />

    <div class="create-fields-checklist">
      <el-checkbox v-model="checkAllToCreate" :indeterminate="isIndeterminateToCreate"
        @change="handlecheckAllToCreateChange">全选</el-checkbox>
      <el-checkbox-group v-model="checkedFieldsToCreate" @change="handleCheckedFieldsToCreateChange">
        <el-checkbox v-for="fieldToCreate in fieldsToCreate" :key="fieldToCreate.key" :label="fieldToCreate.label">{{
          fieldToCreate.key
        }}</el-checkbox>
      </el-checkbox-group>
    </div>
  </div>

  <!-- Only Mapping: -->
  <div v-if="dataWritenType == 'Only Mapping'" class="field-mapping">
    <el-alert style="margin-top: 20px;" title="请勾选需映射的字段" type="info" show-icon />

    <div class="map-fields-checklist">
      <el-checkbox v-model="checkAllToMap" :indeterminate="isIndeterminateToMap"
        @change="handlecheckAllToMapChange">全选</el-checkbox>
      <el-checkbox-group v-model="checkedFieldsToMap" @change="handleCheckedFieldsToMapChange">
        <el-checkbox v-for="fieldToMap in fieldsToMap" :key="fieldToMap.key" :label="fieldToMap.label">{{
          fieldToMap.key
        }}</el-checkbox>
      </el-checkbox-group>
    </div>

    <el-form style="margin-top: 20px;" ref="form2" label-width="80px">
      <el-form-item v-if="isMapped('bookNameV')" label="书名" size="large" required>
        <el-select clearable v-model="bookFieldData.bookNameFId" placeholder="请选择对应字段" style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>
      <el-form-item v-if="isMapped('authorV')" label="作者" size="large" required>
        <el-select clearable v-model="bookFieldData.authorFId" placeholder="请选择对应字段" style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>

      <el-form-item v-if="isMapped('pressDateV')" label="出版时间" size="large" required>
        <el-select clearable v-model="bookFieldData.pressDateFId" placeholder="请选择对应字段" style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>
      <el-form-item v-if="isMapped('picturesV')" label="封面" size="large" required>
        <el-select clearable v-model="bookFieldData.picturesFId" placeholder="请选择对应字段" style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>
      <el-form-item v-if="isMapped('priceV')" label="定价" size="large" required>
        <el-select clearable v-model="bookFieldData.priceFId" placeholder="请选择对应字段" style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>
      <el-form-item v-if="isMapped('bookDescV')" label="简介" size="large" required>
        <el-select clearable v-model="bookFieldData.bookDescFId" placeholder="请选择对应字段" style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>
    </el-form>
  </div>

  <!-- Mixed Mode: -->
  <div v-if="dataWritenType == 'Mixed Mode'" class="field-creation">
    <el-alert title="请勾选需要创建的字段" type="info" show-icon />
    <div class="create-fields-checklist">
      <el-checkbox v-model="checkAllToCreate" :indeterminate="isIndeterminateToCreate"
        @change="handlecheckAllToCreateChange">全选</el-checkbox>
      <el-checkbox-group v-model="checkedFieldsToCreate" @change="handleCheckedFieldsToCreateChange">
        <el-checkbox v-for="fieldToCreate in fieldsToCreate" :key="fieldToCreate.key" :label="fieldToCreate.label">{{
          fieldToCreate.key
        }}</el-checkbox>
      </el-checkbox-group>
    </div>



    <el-alert style="margin: 40px 0 20px 0;" title="请勾选需映射的字段" type="info" show-icon />

    <div class="map-fields-checklist">
      <el-checkbox v-model="checkAllToMap" :indeterminate="isIndeterminateToMap"
        @change="handlecheckAllToMapChange">全选</el-checkbox>
      <el-checkbox-group v-model="checkedFieldsToMap" @change="handleCheckedFieldsToMapChange">
        <el-checkbox v-for="fieldToMap in fieldsToMap" :key="fieldToMap.key" :label="fieldToMap.label">{{
          fieldToMap.key
        }}</el-checkbox>
      </el-checkbox-group>
    </div>

    <el-form style="margin-top: 20px;" ref="form2" label-width="80px">
      <el-form-item v-if="isMapped('bookNameV')" label="书名" size="large" required>
        <el-select clearable v-model="bookFieldData.bookNameFId" placeholder="请选择对应字段" style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>
      <el-form-item v-if="isMapped('authorV')" label="作者" size="large" required>
        <el-select clearable v-model="bookFieldData.authorFId" placeholder="请选择对应字段" style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>

      <el-form-item v-if="isMapped('pressDateV')" label="出版时间" size="large" required>
        <el-select clearable v-model="bookFieldData.pressDateFId" placeholder="请选择对应字段" style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>
      <el-form-item v-if="isMapped('picturesV')" label="封面" size="large" required>
        <el-select clearable v-model="bookFieldData.picturesFId" placeholder="请选择对应字段" style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>
      <el-form-item v-if="isMapped('priceV')" label="定价" size="large" required>
        <el-select clearable v-model="bookFieldData.priceFId" placeholder="请选择对应字段" style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>
      <el-form-item v-if="isMapped('bookDescV')" label="简介" size="large" required>
        <el-select clearable v-model="bookFieldData.bookDescFId" placeholder="请选择对应字段" style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>
    </el-form>

  </div>


  <div class="alert-tip">
    <el-button style="margin: 20px 0;" :disabled="isRequiredBlank" type="primary" @click="writeData">生成图书信息</el-button>
    <el-alert v-if="isDataWriten === 1" title="Success!" type="success" show-icon />
    <el-alert v-if="isDataWriten === 2" :title="requestErrorInfo" type="error" show-icon />
  </div>
</template>

<script>
import { FieldType, bitable } from '@lark-base-open/js-sdk';
import { ref, onMounted, computed, h } from 'vue';
import {
  ElButton,
  ElForm,
  ElFormItem,
  ElSelect,
  ElOption,
  ElInput,
} from 'element-plus';

import axios from 'axios';


export default {
  components: {
    ElButton,
    ElForm,
    ElFormItem,
    ElSelect,
    ElOption,
    ElInput,

  },
  setup() {
    // 数据区域---------
    // -- 辅助数据
    const interfaceAddr = ref('https://jisuisbn.market.alicloudapi.com/isbn/query')
    const appcode = ref('db7bd581534a4da2b13f60713ff83396')
    const dataWritenType = ref('Only Mapping')  // 选择数据写回模式
    const requestErrorInfo = ref('')   // 数据请求结果提示
    const isDataWriten = ref(0)   // 数据请求结果提示の控制变量

    // -- -- Only Creation Mode 下的辅助变量
    const checkAllToCreate = ref(false)
    const isIndeterminateToCreate = ref(true)
    const fieldsToCreate = ref([
      {
        "key": "书名",
        "label": "bookNameV"
      },
      {
        "key": "作者",
        "label": "authorV"
      },
      {
        "key": "出版时间",
        "label": "pressDateV"
      },
      {
        "key": "价格",
        "label": "priceV"
      },
      {
        "key": "封面",
        "label": "picturesV"
      },
      {
        "key": "简介",
        "label": "bookDescV"
      }
    ])   //  可以创建的字段
    const checkedFieldsToCreate = ref(['bookNameV'])  // 默认的to-create的字段

    // -- -- Only Mapping Mode 下的辅助变量
    const checkAllToMap = ref(false)
    const isIndeterminateToMap = ref(true)
    const fieldsToMap = ref([
      {
        "key": "书名",
        "label": "bookNameV"
      },
      {
        "key": "作者",
        "label": "authorV"
      },
      {
        "key": "出版时间",
        "label": "pressDateV"
      },
      {
        "key": "价格",
        "label": "priceV"
      },
      {
        "key": "封面",
        "label": "picturesV"
      },
      {
        "key": "简介",
        "label": "bookDescV"
      }
    ])   //  可以创建的字段
    const checkedFieldsToMap = ref(['authorV'])   // 默认的to-map的字段


    // -- 源数据
    const IsbnFieldId = ref('')   // Isbn字段列ID
    const fieldListSeView = ref([])  // 当前视图下的字段列表



    // -- 写回数据
    const bookData = ref({
      bookNameV: '',
      authorV: '',
      pressDateV: '',
      priceV: '',
      picturesV: '',
      bookDescV: ''
    })  // 写回记录对应的数据值

    const bookFieldData = ref({
      bookNameFId: '',
      authorFId: '',
      pressDateFId: '',
      priceFId: '',
      picturesFId: '',
      bookDescFId: ''
    })  //  写回数据对应的字段列ID




    // 数据预处理区域----------  是否存在必填项为空的情况
    const isRequiredBlank = computed(() => {

      if (dataWritenType.value == 'Only Creation')
        return !(IsbnFieldId.value && interfaceAddr.value && appcode.value)
      else {
        if ((IsbnFieldId.value && interfaceAddr.value && appcode.value) == false) {
          return true
        }
        for (let item of checkedFieldsToMap.value) {
          let key = item.slice(0, -1) + 'FId'
          if (bookFieldData.value[key] == '')
            return true

        }
      }

      return false
    })   // 生成图书信息の控制变量：必填字段是否都已填写   计算属性 

    const getBookData = async (IsbnValue) => {
      const url = `${interfaceAddr.value}?isbn=${IsbnValue}`;

      const config = {
        headers: {
          'Authorization': `APPCODE ${appcode.value}`,
          'Content-Type': 'application/json; charset=UTF-8'
        }
      };

      let res = ''

      await axios.get(url, config)
        .then((response) => {
          isDataWriten.value = 1
          res = response

        }).catch(err => {
          isDataWriten.value = 2
          requestErrorInfo.value = err

        })

      console.log(res)
      if (res.status === 200)
        return res.data.result



    }     // 接口请求  

    const isMapped = (value) => {
      return checkedFieldsToMap.value.includes(value)
    }


    // 算法区域----------
    const getIsbnValue = async (Record) => {
      const selection = await bitable.base.getSelection();
      const table = await bitable.base.getTableById(selection.tableId);
      const IsbnValue = await table.getCellValue(IsbnFieldId.value, Record)

      return IsbnValue
    }     // 获取当前记录的ISBN号




    const createFields = async () => {
      const selection = await bitable.base.getSelection();
      const table = await bitable.base.getTableById(selection.tableId); // 获取当前数据表实例


      for (let toCreateField of checkedFieldsToCreate.value) {
        switch (toCreateField) {
          case 'picturesV':
            bookFieldData.value.picturesFId = await table.addField({
              type: FieldType.Url,
              name: '封面',
            })
            break;
          case 'bookNameV':
            bookFieldData.value.bookNameFId = await table.addField({
              type: FieldType.Text,
              name: '书名',
            })
            break;
          case 'authorV':
            bookFieldData.value.authorFId = await table.addField({
              type: FieldType.Text,
              name: '作者名',
            })
            break;
          case 'pressDateV':
            bookFieldData.value.pressDateFId = await table.addField({
              type: FieldType.Text,
              name: '出版时间',
            })
            break;
          case 'priceV':
            bookFieldData.value.priceFId = await table.addField({
              type: FieldType.Currency,
              name: '定价',
            })
            break;
          case 'bookDescV':
            bookFieldData.value.bookDescFId = await table.addField({
              type: FieldType.Text,
              name: '简介',
            })
            break;
          default:
            break;

        }
      }

      return bookFieldData
    }  // 创建所勾选的字段，和checkedFieldsToCreate（ref vue的双向绑定变量）（选中的字段列表）强相关

    const writeData = async (bookData, fieldId, RecordId) => {

      // == 混合模式下的非空判断  都要选择
      if (dataWritenType.value == 'Mixed Mode') {
        if (checkedFieldsToCreate.value.length == 0) {
          await bitable.ui.showToast({
            toastType: 'warning',
            message: '请勾选所需创建的字段'
          })
          return
        } else if (checkedFieldsToMap.value.length == 0) {
          await bitable.ui.showToast({
            toastType: 'warning',
            message: '请勾选所需映射的字段'
          })
          return
        }
      }

      // == Only Mapping 模式下的非空判断
      if (checkedFieldsToMap.value.length == 0) {
        await bitable.ui.showToast({
          toastType: 'warning',
          message: '请勾选所需映射的字段'
        })
        return
      }

      // == Only Creation 模式下的非空判断
      if (checkedFieldsToCreate.value.length == 0) {
        await bitable.ui.showToast({
          toastType: 'warning',
          message: '请勾选所需创建的字段'
        })
        return
      }


      const { tableId, viewId } = await bitable.base.getSelection();
      const table = await bitable.base.getActiveTable();
      // 全部记录
      // const view = await table.getViewById(viewId);
      // const RecordList = await view.getVisibleRecordIdList()

      // 选择记录会话框
      const RecordList = await bitable.ui.selectRecordIdList(tableId, viewId);


      console.log("RecordList:", RecordList)

      // == 创建字段
      if (dataWritenType.value != 'Only Mapping') {
        try {
          await createFields()
        } catch (error) {
          await bitable.ui.showToast({
            toastType: 'error',
            message: '存在已创建字段，请取消勾选'
          })

          return
        }
      }


      for (let Record of RecordList) {
        console.log("Record", Record)
        let IsbnValue = await getIsbnValue(Record)

        let bookData = await getBookData(IsbnValue)
        console.log(bookData)
        bookData.value = {
          'bookNameV': bookData.title,
          'authorV': bookData.author,
          'pressDateV': bookData.pubdate,
          'priceV': bookData.price,
          'picturesV': bookData.pic,
          'bookDescV': bookData.summary
        }

        console.log("bookData:", bookData.value)

        if (dataWritenType.value != 'Only Mapping') {
          for (let toCreateField of checkedFieldsToCreate.value) {
            switch (toCreateField) {
              case 'picturesV':
                await table.setCellValue(bookFieldData.value.picturesFId, Record, [{ type: 'url', text: bookData.value.picturesV }])
                break;
              case 'bookNameV':
                await table.setCellValue(bookFieldData.value.bookNameFId, Record, [{ type: 'text', text: bookData.value.bookNameV }])
                break;
              case 'authorV':
                await table.setCellValue(bookFieldData.value.authorFId, Record, [{ type: 'text', text: bookData.value.authorV }])
                break;
              case 'pressDateV':
                await table.setCellValue(bookFieldData.value.pressDateFId, Record, [{ type: 'text', text: bookData.value.pressDateV }])
                break;
              case 'priceV':
                await table.setCellValue(bookFieldData.value.priceFId, Record, Number(bookData.value.priceV).toFixed(2))
                break;
              case 'bookDescV':
                await table.setCellValue(bookFieldData.value.bookDescFId, Record, [{ type: 'text', text: bookData.value.bookDescV }])
                break;
              default:
                break;

            }
          }
        }

        if (dataWritenType.value != 'Only Creation') {
          for (let toMapField of checkedFieldsToMap.value) {
            switch (toMapField) {
              case 'picturesV':
                await table.setCellValue(bookFieldData.value.picturesFId, Record, [{ type: 'url', text: bookData.value.picturesV }])
                break;
              case 'bookNameV':
                await table.setCellValue(bookFieldData.value.bookNameFId, Record, [{ type: 'text', text: bookData.value.bookNameV }])
                break;
              case 'authorV':
                await table.setCellValue(bookFieldData.value.authorFId, Record, [{ type: 'text', text: bookData.value.authorV }])
                break;
              case 'pressDateV':
                await table.setCellValue(bookFieldData.value.pressDateFId, Record, [{ type: 'text', text: bookData.value.pressDateV }])
                break;
              case 'priceV':
                await table.setCellValue(bookFieldData.value.priceFId, Record, Number(bookData.value.priceV).toFixed(2))
                break;
              case 'bookDescV':
                await table.setCellValue(bookFieldData.value.bookDescFId, Record, [{ type: 'text', text: bookData.value.bookDescV }])
                break;
              default:
                break;
            }
          }
        }



      }

      return true
    }


    // 数据后处理区域---------
    // Create==全选事件
    const handlecheckAllToCreateChange = (val) => {
      const data = JSON.parse(JSON.stringify(fieldsToCreate.value))
      if (val) {
        for (const item of data)
          checkedFieldsToCreate.value.push(item.label);


      } else {
        checkedFieldsToCreate.value = []
      }
      isIndeterminateToCreate.value = false
    }
    // Create==字段选择事件
    const handleCheckedFieldsToCreateChange = (value) => {
      const checkedCount = value.length
      checkAllToCreate.value = checkedCount === fieldsToCreate.value.length
      isIndeterminateToCreate.value = checkedCount > 0 && checkedCount < fieldsToCreate.value.length
      console.log('checkedFieldsToCreate:', checkedFieldsToCreate.value)

    }

    // Map==全选事件
    const handlecheckAllToMapChange = (val) => {
      const data = JSON.parse(JSON.stringify(fieldsToMap.value))
      if (val) {
        for (const item of data)
          checkedFieldsToMap.value.push(item.label);


      } else {
        checkedFieldsToMap.value = []
      }
      isIndeterminateToMap.value = false
    }
    // Map==字段选择事件
    const handleCheckedFieldsToMapChange = (value) => {
      const checkedCount = value.length
      checkAllToMap.value = checkedCount === fieldsToMap.value.length
      isIndeterminateToMap.value = checkedCount > 0 && checkedCount < fieldsToMap.value.length
      console.log('checkedFieldsToMap:', checkedFieldsToMap.value)

    }





    // 页面声明周期区域----------

    onMounted(async () => {

      const selection = await bitable.base.getSelection()
      const table = await bitable.base.getTableById(selection.tableId)
      const view = await table.getViewById(selection.viewId)
      fieldListSeView.value = await view.getFieldMetaList()


    })

    return {
      interfaceAddr,
      appcode,
      dataWritenType,
      isDataWriten,
      isRequiredBlank,
      requestErrorInfo,
      IsbnFieldId,
      checkedFieldsToCreate,
      checkedFieldsToMap,
      fieldsToCreate,
      fieldsToMap,
      isIndeterminateToCreate,
      isIndeterminateToMap,
      checkAllToCreate,
      checkAllToMap,
      fieldListSeView,
      bookData,
      bookFieldData,
      writeData,
      handlecheckAllToCreateChange,
      handleCheckedFieldsToCreateChange,
      handlecheckAllToMapChange,
      handleCheckedFieldsToMapChange,
      isMapped
    }
  }
};
</script>

<style scoped>
.write-data-tip {
  margin-top: 40px;
  margin-bottom: 20px;
  font-size: 14px;
  color: var(--el-text-color-regular);
  border-left: 4px solid #389aff;
  padding-left: 10px;
}

.field-creation {
  margin: 20px 0;
}

.el-alert:first-child {
  margin: 0 0 20px 0;
}


.field-mapping {
  margin-top: 20px 0;
}
</style>
