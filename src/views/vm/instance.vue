<template>
  <div>
    <div style="background: #ffffff">
      <a-page-header
        style="border: 1px solid rgb(235, 237, 240)"
        title="实例详情"
        sub-title="windows"
        @back="() => $router.go(-1)"
      >
        <div>
          <template slot="extra">
            <a-button key="3"> 关机 </a-button>
            <a-button key="2" type="primary"> 重启 </a-button>
          </template>
          <div class="content">
            <div class="extra">
              <div
                :style="{
                  display: 'flex',
                  width: 'max-content',
                  justifyContent: 'flex-end',
                }"
              >
                <a-statistic
                  title="状态"
                  value="运行中"
                  :style="{
                    marginRight: '32px',
                  }"
                />
              </div>
            </div>
          </div>
        </div>
        <template slot="footer">
          <a-tabs default-active-key="1">
            <a-tab-pane key="1" tab="概要">
              <a-row :gutter="16">
                <a-col :xs="24" :sm="24" :md="24" :lg="8" :xl="8">
                  <a-card title="实例详情" class="card-item">
                    <div class="main">
                      <a-descriptions size="small" :column="1">
                        <a-descriptions-item label="实例名称"> windows </a-descriptions-item>
                        <a-descriptions-item label="部署区域"> 东亚 </a-descriptions-item>
                        <a-descriptions-item label="实例规格"> B1S-CPU: 1核 内存: 2GB </a-descriptions-item>
                        <a-descriptions-item label="创建时间"> 2017-01-10 </a-descriptions-item>
                        <a-descriptions-item label="磁盘大小"> 50GB </a-descriptions-item>
                        <a-descriptions-item label="操作系统"> Ubuntu Lts 20.0.4 </a-descriptions-item>
                      </a-descriptions>
                    </div>
                  </a-card>
                </a-col>
                <a-col :xs="24" :sm="24" :md="24" :lg="8" :xl="8">
                  <a-card title="资源使用" class="card-item">
                    <a-progress type="dashboard" :format="(percent) => `CPU ${percent} %`" :percent="32" :width="100" />
                    <a-progress
                      type="dashboard"
                      :format="(percent) => `内存 ${percent} % 20MB/2GB`"
                      :percent="46"
                      :width="100"
                      style="margin: 20px 20px"
                    />
                    <p>磁盘<a-progress :percent="30" /> 2GB/64GB</p>
                  </a-card>
                </a-col>
                <a-col :xs="24" :sm="24" :md="24" :lg="8" :xl="8">
                  <a-card title="更多操作" class="card-item">
                    <a-button key="2" type="danger"> 删除实例 </a-button>
                  </a-card>
                </a-col>
              </a-row>
            </a-tab-pane>
            <a-tab-pane key="2" tab="远程登陆" />
          </a-tabs>
        </template>
      </a-page-header>
    </div>
  </div>
</template>

  <script>
  export default {
    data () {
      return {
        dataList: {}
      }
    },
    mounted () {
    },
    methods: {}
  }
  </script>

  <style>
  tr:last-child td {
    padding-bottom: 0;
  }
  #components-page-header-demo-responsive .content {
    display: flex;
  }
  #components-page-header-demo-responsive .ant-statistic-content {
    font-size: 20px;
    line-height: 28px;
  }
  @media (max-width: 576px) {
    #components-page-header-demo-responsive .content {
      display: block;
    }

    #components-page-header-demo-responsive .main {
      width: 100%;
      margin-bottom: 12px;
    }

    #components-page-header-demo-responsive .extra {
      width: 100%;
      margin-left: 0;
      text-align: left;
    }
  }
  .card-item {
    margin: 40px 0px;
  }
  </style>
