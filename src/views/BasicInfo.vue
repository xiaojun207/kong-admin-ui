<template>
  <div>
    <Breadcrumb>
      <BreadcrumbItem to="/">{{ $t('breadcrumb.home') }}</BreadcrumbItem>
      <BreadcrumbItem>{{ $t('breadcrumb.info') }}</BreadcrumbItem>
    </Breadcrumb>
    <h1 style="width: 100px;height: 10px"></h1>

    <h3>{{ $t('breadcrumb.info') }}:</h3>
    <Row>
      <Col span="8">hostname: {{ basicInfo.hostname }}</Col>
      <Col span="8">version: {{ basicInfo.version }}</Col>
      <Col span="8">lua_version: {{ basicInfo.lua_version }}</Col>
    </Row>
    <Row>
      <Col span="8">node_id: {{ basicInfo.node_id }}</Col>
      <Col span="8">tagline: {{ basicInfo.tagline }}</Col>
    </Row>

    <h3>{{ $t('basicInfo.configuration') }}:</h3>
    <JsonView :json="basicInfo.configuration"></JsonView>

    <h3>{{ $t('basicInfo.status') }}:</h3>
    <JsonView :json="status"></JsonView>

    <h3>{{ $t('basicInfo.plugin') }}:</h3>
    <JsonView :json="basicInfo.plugins"></JsonView>

    <h3>{{ $t('basicInfo.pids') }}:</h3>
    <JsonView :json="basicInfo.pids"></JsonView>

    <h3>{{ $t('basicInfo.timers') }}:</h3>
    <JsonView :json="basicInfo.timers"></JsonView>

<!--    <JsonView :json="basicInfo"></JsonView>-->
  </div>
</template>

<script>
import EventBus from '@/event-bus'
import JsonView from '@/components/JsonView'

export default {
  data() {
    return {
      basicInfo: {
        configuration: {},
        plugins: {},
        timers: {},
        pids: {}
      },
      status: {
        server: {}
      },
      timer: ''
    }
  },
  components:{
    'JsonView':JsonView
  },
  mounted() {
    EventBus.$emit('changePage', {activeName: 'index', openNames: []});
    this.loadBasicInfo();
    this.loadStatus();
    this.timer = setInterval(this.loadStatus, 3000);
  },
  methods: {
    loadBasicInfo() {
      let _this = this;
      this._get('/', response => {
        _this.basicInfo = response.data;
        localStorage.kongVersion = _this.basicInfo.version;
      });
    },
    loadStatus() {
      let _this = this;
      this._get('/status', response => {
        _this.status = response.data;
      });
    }
  },
  destroyed() {
    clearInterval(this.timer);
  }
}
</script>

<style scoped>
#login {
  margin-top: 200px;
}

input {
  margin-left: 20px;
}

Col {
  padding: 50px;
}
</style>
