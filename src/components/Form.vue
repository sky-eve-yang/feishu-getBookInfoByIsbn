

<template>
  <el-form ref="form" :model="formData" label-width="60px">

    <!-- 3. 接口地址 -->
    <el-form-item label="Api" size="large" required>

      <!-- {{ $t('form.label.address') }}<el-input v-model="interfaceAddr" :placeholder="$t('form.label.addressInput')" /> -->
      {{ $t('form.label.appcode') }}<el-input type="password" v-model="appcode"
        :placeholder="$t('form.label.appcodeInput')" />
      <el-link style="color: #3e75f5;" type="primary" href="https://jfsq6znqku.feishu.cn/docx/QiEbd3LIgoMfDDxKHMwcNyW4n4M?from=from_copylink"
        target="_blank">{{ $t('form.label.apiDocument') }}</el-link>
    </el-form-item>

    <!-- 2. 选择 isbn 字段 -->
    <el-form-item label="ISBN" size="large" required>
      <el-select v-model="IsbnFieldId" :placeholder="$t('form.label.isbnSelectTip')" style="width: 100%">
        <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
      </el-select>
    </el-form-item>

  </el-form>

  <!-- 3.5 选择数据写入的方式：1-字段映射  2-创建字段 -->
  <!-- 4. 写回多维数据表格 -->
  <div class="write-data-tip">{{ $t('form.writeData.desc') }}</div>
  <el-radio-group v-model="dataWritenType" size="default">
    <el-radio-button label="1">{{ $t('form.writeData.mode.creation') }}</el-radio-button>
    <el-radio-button label="2">{{ $t('form.writeData.mode.mapping') }}</el-radio-button>
    <el-radio-button label="3">{{ $t('form.writeData.mode.mixed') }}</el-radio-button>
  </el-radio-group>

  <!-- Only Creation: -->
  <div v-if="dataWritenType == 1" class="field-creation">
    <el-alert style="background-color: #e1eaff;color: #606266;" :title="$t('form.writeData.createTip')" type="info" show-icon />

    <div class="create-fields-checklist">
      <el-checkbox v-model="checkAllToCreate" :indeterminate="isIndeterminateToCreate"
        @change="handlecheckAllToCreateChange">{{ $t('form.writeData.selectAll') }}</el-checkbox>
      <el-checkbox-group v-model="checkedFieldsToCreate" @change="handleCheckedFieldsToCreateChange">
        <el-checkbox v-for="fieldToCreate in fieldsToCreate" :key="fieldToCreate.key" :label="fieldToCreate.label">
          {{ $t(`form.writeData.bookInfo.${fieldToCreate.key}`) }}
        </el-checkbox>
      </el-checkbox-group>
    </div>
  </div>

  <!-- Only Mapping: -->
  <div v-if="dataWritenType == 2" class="field-mapping">
    <el-alert style="margin-top: 20px;background-color: #e1eaff;color: #606266;" :title="$t('form.writeData.mapTip')" type="info" show-icon />

    <div class="map-fields-checklist">
      <el-checkbox v-model="checkAllToMap" :indeterminate="isIndeterminateToMap" @change="handlecheckAllToMapChange">{{
        $t('form.writeData.selectAll') }}</el-checkbox>
      <el-checkbox-group v-model="checkedFieldsToMap" @change="handleCheckedFieldsToMapChange">
        <el-checkbox v-for="fieldToMap in fieldsToMap" :key="fieldToMap.key" :label="fieldToMap.label">
          {{ $t(`form.writeData.bookInfo.${fieldToMap.key}`) }}
        </el-checkbox>
      </el-checkbox-group>
    </div>

    <el-form style="margin-top: 20px;" ref="form2" label-width="80px">
      <el-form-item v-if="isMapped('bookNameV')" :label="$t(`form.writeData.bookInfo.书名`)" size="large" required>
        <el-select clearable v-model="bookFieldData.bookNameFId" :placeholder="$t('form.writeData.selectFieldTip')"
          style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>
      <el-form-item v-if="isMapped('authorV')" :label="$t(`form.writeData.bookInfo.作者`)" size="large" required>
        <el-select clearable v-model="bookFieldData.authorFId" :placeholder="$t('form.writeData.selectFieldTip')"
          style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>
      <el-form-item v-if="isMapped('publisherV')" :label="$t(`form.writeData.bookInfo.出版社`)" size="large" required>
        <el-select clearable v-model="bookFieldData.publisherFId" :placeholder="$t('form.writeData.selectFieldTip')"
          style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>

      <el-form-item v-if="isMapped('pressDateV')" :label="$t(`form.writeData.bookInfo.出版时间`)" size="large" required>
        <el-select clearable v-model="bookFieldData.pressDateFId" :placeholder="$t('form.writeData.selectFieldTip')"
          style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>
      <el-form-item v-if="isMapped('picturesV')" :label="$t(`form.writeData.bookInfo.封面`)" size="large" required>
        <el-select clearable v-model="bookFieldData.picturesFId" :placeholder="$t('form.writeData.selectFieldTip')"
          style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>
      <el-form-item v-if="isMapped('priceV')" :label="$t(`form.writeData.bookInfo.价格`)" size="large" required>
        <el-select clearable v-model="bookFieldData.priceFId" :placeholder="$t('form.writeData.selectFieldTip')"
          style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>
      <el-form-item v-if="isMapped('bookDescV')" :label="$t(`form.writeData.bookInfo.简介`)" size="large" required>
        <el-select clearable v-model="bookFieldData.bookDescFId" :placeholder="$t('form.writeData.selectFieldTip')"
          style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>
    </el-form>
  </div>

  <!-- Mixed Mode: -->
  <div v-if="dataWritenType == 3" class="field-creation">
    <el-alert style="background-color: #e1eaff;color: #606266;" :title="$t('form.writeData.createTip')" type="info" show-icon />
    <div class="create-fields-checklist">
      <el-checkbox v-model="checkAllToCreate" :indeterminate="isIndeterminateToCreate"
        @change="handlecheckAllToCreateChange">{{ $t('form.writeData.selectAll') }}</el-checkbox>
      <el-checkbox-group v-model="checkedFieldsToCreate" @change="handleCheckedFieldsToCreateChange">
        <el-checkbox v-for="fieldToCreate in fieldsToCreate" :key="fieldToCreate.key" :label="fieldToCreate.label">{{
          $t(`form.writeData.bookInfo.${fieldToCreate.key}`)
        }}</el-checkbox>
      </el-checkbox-group>
    </div>



    <el-alert style="margin: 40px 0 20px 0;background-color: #e1eaff;color: #606266;" :title="$t('form.writeData.mapTip')" type="info" show-icon />

    <div class="map-fields-checklist">
      <el-checkbox v-model="checkAllToMap" :indeterminate="isIndeterminateToMap" @change="handlecheckAllToMapChange">{{
        $t('form.writeData.selectAll') }}</el-checkbox>
      <el-checkbox-group v-model="checkedFieldsToMap" @change="handleCheckedFieldsToMapChange">
        <el-checkbox v-for="fieldToMap in fieldsToMap" :key="fieldToMap.key" :label="fieldToMap.label">{{
          $t(`form.writeData.bookInfo.${fieldToMap.key}`)
        }}</el-checkbox>
      </el-checkbox-group>
    </div>

    <el-form style="margin-top: 20px;" ref="form2" label-width="80px">
      <el-form-item v-if="isMapped('bookNameV')" :label="$t(`form.writeData.bookInfo.书名`)" size="large" required>
        <el-select clearable v-model="bookFieldData.bookNameFId" :placeholder="$t('form.writeData.selectFieldTip')"
          style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>
      <el-form-item v-if="isMapped('authorV')" :label="$t(`form.writeData.bookInfo.作者`)" size="large" required>
        <el-select clearable v-model="bookFieldData.authorFId" :placeholder="$t('form.writeData.selectFieldTip')"
          style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>

      <el-form-item v-if="isMapped('pressDateV')" :label="$t(`form.writeData.bookInfo.出版时间`)" size="large" required>
        <el-select clearable v-model="bookFieldData.pressDateFId" :placeholder="$t('form.writeData.selectFieldTip')"
          style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>
      <el-form-item v-if="isMapped('picturesV')" :label="$t(`form.writeData.bookInfo.封面`)" size="large" required>
        <el-select clearable v-model="bookFieldData.picturesFId" :placeholder="$t('form.writeData.selectFieldTip')"
          style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>
      <el-form-item v-if="isMapped('priceV')" :label="$t(`form.writeData.bookInfo.价格`)" size="large" required>
        <el-select clearable v-model="bookFieldData.priceFId" :placeholder="$t('form.writeData.selectFieldTip')"
          style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>
      <el-form-item v-if="isMapped('bookDescV')" :label="$t(`form.writeData.bookInfo.简介`)" size="large" required>
        <el-select clearable v-model="bookFieldData.bookDescFId" :placeholder="$t('form.writeData.selectFieldTip')"
          style="width: 100%">
          <el-option v-for="meta in fieldListSeView" :key="meta.id" :label="meta.name" :value="meta.id" />
        </el-select>
      </el-form-item>
    </el-form>

  </div>


  <div class="alert-tip">
    <div>
      <el-tooltip class="box-item" effect="dark" :content="$t('generateModeInfo')" placement="top-start">
        <el-checkbox v-model="isSelectPartRecords" :label="$t('generateMode')" size="large" />
      </el-tooltip>
    </div>

    <el-button color="#3370ff" style="margin: 20px 0;;" :disabled="isRequiredBlank" type="primary" @click="writeData">{{ $t('generate')
    }}</el-button>
    <el-alert v-if="isDataWriten === 1" title="Success!" type="success" show-icon />
    <el-alert v-if="isDataWriten === 2" :title="requestErrorInfo" type="error" show-icon />
  </div>
</template>

<script>
import { FieldType, bitable } from '@lark-base-open/js-sdk';
import { ref, onMounted, computed, h } from 'vue';
import { useI18n } from 'vue-i18n';
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
    const { t } = useI18n();

    // 数据区域---------
    // -- 辅助数据
    const interfaceAddr = ref('https://get-book-info-by-isbn-backend.replit.app')
    const appcode = ref('')
    const dataWritenType = ref(2)  // 选择数据写回模式: 1-创建模式  2-映射模式  3-混合模式
    const requestErrorInfo = ref('')   // 数据请求结果提示
    const isDataWriten = ref(0)   // 数据请求结果提示の控制变量
    const isSelectPartRecords = ref(false)  // 是否应用于整个table表  true-是  false-选择交互

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
        "key": "出版社",
        "label": "publisherV"
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
        "key": "出版社",
        "label": "publisherV"
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
      bookDescV: '',
      publisherV: ''
    })  // 写回记录对应的数据值

    const bookFieldData = ref({
      bookNameFId: '',
      authorFId: '',
      pressDateFId: '',
      priceFId: '',
      picturesFId: '',
      bookDescFId: '',
      publisherFId: ''
    })  //  写回数据对应的字段列ID




    // 数据预处理区域----------  是否存在必填项为空的情况
    const isRequiredBlank = computed(() => {

      if (dataWritenType.value == 1)
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
      const url = `${interfaceAddr.value}/fetch_book_info`;




      const config = {
        params: {
          isbn: IsbnValue,
          appcode: appcode.value
        }
      };

      let res = ''

      await axios.get(url, config)
        .then((response) => {
          isDataWriten.value = 1
          res = response

        }).catch(err => {
          console.log("err", err)
          // isDataWriten.value = 2
          // requestErrorInfo.value = err

        })

      console.log("res", res)
      if (res.status === 200)
        return res.data.result
      else
        return "ERROR"


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
              name: t(`form.writeData.bookInfo.封面`),
            })
            break;
          case 'bookNameV':
            bookFieldData.value.bookNameFId = await table.addField({
              type: FieldType.Text,
              name: t(`form.writeData.bookInfo.书名`),
            })
            break;
          case 'authorV':
            bookFieldData.value.authorFId = await table.addField({
              type: FieldType.Text,
              name: t(`form.writeData.bookInfo.作者`),
            })
            break;
          case 'publisherV':
            bookFieldData.value.publisherFId = await table.addField({
              type: FieldType.Text,
              name: t(`form.writeData.bookInfo.出版社`),
            })
            break;
          case 'pressDateV':
            bookFieldData.value.pressDateFId = await table.addField({
              type: FieldType.Text,
              name: t(`form.writeData.bookInfo.出版时间`),
            })
            break;
          case 'priceV':
            bookFieldData.value.priceFId = await table.addField({
              type: FieldType.Currency,
              name: t(`form.writeData.bookInfo.价格`),
            })
            break;
          case 'bookDescV':
            bookFieldData.value.bookDescFId = await table.addField({
              type: FieldType.Text,
              name: t(`form.writeData.bookInfo.简介`),
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
      if (dataWritenType.value == 3) {
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

      setLocalStorage()


      const { tableId, viewId } = await bitable.base.getSelection();
      const table = await bitable.base.getActiveTable();
      const view = await table.getViewById(viewId);

      let RecordList = []

      if (isSelectPartRecords.value) {
        // 选择记录会话框
        RecordList = await bitable.ui.selectRecordIdList(tableId, viewId);
      } else {
        // 全部记录
        RecordList = await view.getVisibleRecordIdList()
      }
      

      


      console.log("RecordList:", RecordList)

      // == 创建字段
      if (dataWritenType.value != 2) {
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
        console.log("IsbnValue", IsbnValue)

        let requsetBookData = await getBookData(IsbnValue)
        console.log("bookData:", bookData)

        if (requsetBookData == 'ERROR') {
          bookData.value = {
          'bookNameV': t('requestErrorTip'),
          'authorV':t('requestErrorTip'),
          'publisherV': t('requestErrorTip'),
          'pressDateV': t('requestErrorTip'),
          'priceV': 0,
          'picturesV': t('requestErrorTip'),
          'bookDescV': t('requestErrorTip')
          }
        } else {
          bookData.value = {
            'bookNameV': requsetBookData.title,
            'authorV': requsetBookData.author,
            'publisherV': requsetBookData.publisher,
            'pressDateV': requsetBookData.pubdate,
            'priceV': requsetBookData.price,
            'picturesV': requsetBookData.pic,
            'bookDescV': requsetBookData.summary
          }
        }
        

        if (dataWritenType.value != 2) {
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
              case 'publisherV':
                await table.setCellValue(bookFieldData.value.publisherFId, Record, [{ type: 'text', text: bookData.value.publisherV }])
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

        if (dataWritenType.value != 1) {
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
              case 'publisherV':
                await table.setCellValue(bookFieldData.value.publisherFId, Record, [{ type: 'text', text: bookData.value.publisherV }])
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

    /**
 * @common(set) {读取缓存，并赋值给相关变量}
 */
const setLocalStorage = () => {

  localStorage.setItem('appcode', appcode.value)   // string 类型
  localStorage.setItem('dataWritenType', dataWritenType.value)   // string 类型
  localStorage.setItem('IsbnFieldId', IsbnFieldId.value)   // string 类型
  
}

    
    /**
 * @common(set) {读取缓存，并赋值给相关变量}
 */
  const setVariableFromLocalStorage = () => {

    if (localStorage.getItem('appcode') !== null) {  // string 类型
      appcode.value = localStorage.getItem('appcode')
    }
    if (localStorage.getItem('dataWritenType') !== null) {  // string 类型
      dataWritenType.value = localStorage.getItem('dataWritenType')
    }
    if (localStorage.getItem('IsbnFieldId') !== null) {  // string 类型
      IsbnFieldId.value = localStorage.getItem('IsbnFieldId')
    }
    

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

      setVariableFromLocalStorage()
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
      isMapped,
      isSelectPartRecords
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

:deep(.el-icon) {
  color: #3370ff !important;
}

:deep(.el-radio-button__original-radio:checked+.el-radio-button__inner) {
  background-color: #3370ff;
}

:deep(.el-radio-button__inner:hover) {
  color: #3e75f5;
}

:deep(.el-radio-button__original-radio:checked+.el-radio-button__inner:hover) {
  color: #fff;
}

</style>
