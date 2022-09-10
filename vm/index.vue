<template>
    <page-header-wrapper content="开始便捷的管理所有虚拟机，只需要几步操作即可！">
      <a-list rowKey="id" :grid="{ gutter: 24, lg: 3, md: 2, sm: 1, xs: 1 }" :dataSource="dataSource">
        <a-list-item slot="renderItem" slot-scope="item">
          <template v-if="!item || item.ip === undefined">
            <a-button class="new-btn" type="dashed" @click="showModal">
              <a-icon type="plus" />
              新建虚拟机
            </a-button>
          </template>
          <template v-else>
            <a-card :hoverable="true">
              <router-link to="/vm/instance">
              <a-card-meta>
                <a slot="title">{{ item.name }} <a-tag :color="returnStatusClass(item.status)">{{ item.status }}</a-tag></a>
                <a-avatar class="card-avatar" slot="avatar" :src="returnIcon(item.os)" size="large" />
                <div class="meta-content" slot="description" style="margin: 10px 0px">{{ item.speci }}</div>
                <div class="meta-content" slot="description" style="margin: 10px 0px">公网IP：{{ item.ip }}</div>
              </a-card-meta>
              </router-link>
              <template class="ant-card-actions" slot="actions">
                <a>关机</a>
                <a>重启</a>
                <a>更多</a>
              </template>
            </a-card>
   
          </template>
        </a-list-item>
      </a-list>
      <a-modal
        :visible="visible"
        :confirm-loading="confirmLoading"
        title="创建虚拟机"
        okText="创建"
        cancelText="取消"
        @cancel="handleCancel"
        @ok="handleOk"
      >
        <a-form layout="vertical" :form="form" ref="collectionForm">
          <a-form-item label="虚拟机名称">
            <a-input
              v-decorator="[
                'name',
                {
                  rules: [{ required: true, message: '请输入虚拟机名称' }],
                },
              ]"
              placeholder="请输入虚拟机名称，不可重复"
            />
          </a-form-item>
          <a-form-item label="地区">
            <a-select
              v-decorator="['location', { rules: [{ required: true, message: '请选择地区' }] }]"
              placeholder="请选择你要创建的虚拟机的地区"
            >
              <a-select-option value="estatus"> 美国东部 </a-select-option>
              <a-select-option value="eaisa"> 东亚 </a-select-option>
            </a-select>
          </a-form-item>
          <a-form-item class="collection-create-form_last-form-item" label="系统类型">
            <a-radio-group
              v-decorator="[
                'osType',
                {
                  initialValue: 'Linux',
                },
              ]"
              @change="osTypeChange"
            >
              <a-radio value="Windows">Windows</a-radio>
              <a-radio value="Linux">Linux</a-radio>
            </a-radio-group>
          </a-form-item>
          <a-form-item label="操作系统">
            <a-select
              v-decorator="['os', { rules: [{ required: true, message: '请选择操作系统' }] }]"
              placeholder="请选操作系统"
            >
              <a-select-option v-for="item in osList" v-bind:key="item.key" :value="item.value">
                {{ item.key }}
              </a-select-option>
            </a-select>
          </a-form-item>
          <a-form-item label="磁盘大小">
            <a-input
              v-decorator="[
                'disk',
                {
                  rules: [{ required: true, message: '磁盘大小不可为空' }],
                },
              ]"
              placeholder="请输入磁盘大小（单位：GB）"
            />
          </a-form-item>
          <a-form-item label="SSH用户名">
            <a-input
              v-decorator="[
                'username',
                {
                  rules: [{ required: true, message: 'SSH用户名不可为空' }],
                },
              ]"
              placeholder="请输入SSH用户名（不能是root或administrator等保留字）"
            />
          </a-form-item>
          <a-form-item label="SSH密码">
            <a-input
              v-decorator="[
                'password',
                {
                  rules: [{ required: true, message: 'SSH密码不可为空' }],
                },
              ]"
              type="password"
              placeholder="请输入SSH密码"
            />
          </a-form-item>
        </a-form>
      </a-modal>
    </page-header-wrapper>
  </template>
  
  <script>
  import { PageHeaderWrapper } from '@ant-design-vue/pro-layout'
  import { getVMList } from '@/api/vm'
  export default {
    name: 'Vm',
    components: {
      PageHeaderWrapper,
    },
    data() {
      return {
        dataSource: [],
        visible: false,
        confirmLoading: false,
        form: this.$form.createForm(this, { name: 'form_in_modal' }),
        linuxList: [
          { key: 'ubuntu lts 20.0.4', value: 'ubuntu lts 20.0.4' },
          { key: 'ubuntu lts 18.0.6', value: 'ubuntu lts 18.0.6' },
          { key: 'centos 7.4', value: 'centos 7.4' },
          { key: 'centos 8.0', value: 'centos 8.0' },
        ],
        winList: [
          { key: 'windows server 2008', value: 'windows server 2008' },
          { key: 'windows server 2012', value: 'windows server 2012' },
          { key: 'windows server 2016', value: 'windows server 2016' },
        ],
        osList: [],
      }
    },
    created(){
      this.dataSource.push({})
      getVMList().then(res => {
        console.log(res)
        for(var i=0;i<res.length;i++){
          this.dataSource.push(res[i])
        }
      })
    },
    mounted() {
      /*this.dataSource.push({
        id: 0,
        title: 'Windows',
        avatar: require('/public/win.png'),
        size: 'B1S - CPU 1核 - 内存 1GB - 系统盘 30GB',
        ip: '127.0.0.1',
      })*/
      this.osList.splice(0, this.osList.length)
      for (var i = 0; i < this.linuxList.length; i++) {
        this.osList.push(this.linuxList[i])
      }
    },
    methods: {
      // 操作系统类型切换
      osTypeChange(n) {
        this.form.setFieldsValue({
          os:'',
        })
        this.osList.splice(0, this.osList.length)
        var osType = n.target.value
        if (osType == 'Linux') {
          for (var i = 0; i < this.linuxList.length; i++) {
            this.osList.push(this.linuxList[i])
          }
        } else {
          for (var i = 0; i < this.winList.length; i++) {
            this.osList.push(this.winList[i])
          }
        }
      },
      showModal() {
        this.visible = true
      },
      handleOk(e) {
        this.ModalText = 'The modal will be closed after two seconds'
        const form = this.$refs.collectionForm.form
        form.validateFields((err, values) => {
          if (err) {
            return
          }
          this.confirmLoading = true
          console.log('Received values of form: ', values)
          form.resetFields()
          this.visible = false
          this.confirmLoading = false
        })
      },
      handleCancel(e) {
        this.visible = false
      },
      //返回运行状态class
      returnStatusClass(v){
        switch (v){
          case "运行中":
            return "green"
            break
          case "已停止":
            return "red"
            break
          default:
            return "yellow"
            break
        }
      },
      returnIcon(v){
        //win
        var reg_win = new RegExp("windows", 'i');
        var isWindows = v.match(reg_win);
  
        //ubuntu
        var reg_ubuntu = new RegExp("ubuntu", 'i');
        var isUbuntu = v.match(reg_ubuntu);
        
        //centos
        var reg_centos = new RegExp("centos", 'i');
        var isCentos = v.match(reg_centos);
  
        if(isWindows){
          return require('/public/windows.jpeg')
        }else if(isUbuntu){
          return require('/public/ubuntu.jpeg')
        }else if(isCentos){
          return require('/public/centos.jpeg')
        }else{
          return require('/public/linux.jpeg')
        }
      }
    },
  }
  </script>
  
  <style lang="less" scoped>
  @import '~@/components/index.less';
  .card-list {
    /deep/ .ant-card-body:hover {
      .ant-card-meta-title > a {
        color: @primary-color;
      }
    }
  
    /deep/ .ant-card-meta-title {
      margin-bottom: 12px;
  
      & > a {
        display: inline-block;
        max-width: 100%;
        color: rgba(0, 0, 0, 0.85);
      }
    }
  
    /deep/ .meta-content {
      position: relative;
      overflow: hidden;
      text-overflow: ellipsis;
      display: -webkit-box;
      height: 64px;
      -webkit-line-clamp: 3;
      -webkit-box-orient: vertical;
  
      margin-bottom: 1em;
    }
  }
  
  .card-avatar {
    width: 48px;
    height: 48px;
    border-radius: 48px;
  }
  
  .ant-card-actions {
    background: #f7f9fa;
  
    li {
      float: left;
      text-align: center;
      margin: 12px 0;
      color: rgba(0, 0, 0, 0.45);
      width: 50%;
  
      &:not(:last-child) {
        border-right: 1px solid #e8e8e8;
      }
  
      a {
        color: rgba(0, 0, 0, 0.45);
        line-height: 22px;
        display: inline-block;
        width: 100%;
        &:hover {
          color: @primary-color;
        }
      }
    }
  }
  
  .new-btn {
    background-color: #fff;
    border-radius: 2px;
    width: 100%;
    height: 185px;
  }
  </style>
  