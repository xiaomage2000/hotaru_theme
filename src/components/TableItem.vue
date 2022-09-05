<template>
  <tr class="tableRow" @click="collapsed = !collapsed">
    <td>
      <div class="ui progress" :class="{'success': getStatus, 'error': !getStatus}">
        <div class="bar" style="width: 100%"><span> {{ getStatus ? '运行中' : '维护中' }} </span>
        </div>
      </div>
    </td>
    <td>{{ server.alias }}</td>
    <td>{{ server.type }}</td>
    <td>{{ server.location }}</td>
    <td>{{ server.uptime || '–' }}</td>
    <td>{{ getStatus ? server.load_1 : '-' }}</td>
    <td>{{
        getStatus ? `${tableRowByteConvert(server.network_rx)} | ${tableRowByteConvert(server.network_tx)}` : '–'
      }}
    </td>
    <td>{{
        getStatus ? `${tableRowByteConvert(server.network_in - server.last_network_in)} | ${tableRowByteConvert(server.network_out - server.last_network_out)}` : '–'
      }}
    </td>
    <td>
      <div class="ui progress" :class="getStatus ? getProcessBarStatus(getCpuStatus) : 'error'">
        <div class="bar" :style="{'width': `${getCpuStatus.toString()}%`}">
          {{ getStatus ? `${getCpuStatus.toString()}%` : '维护中' }}
        </div>
      </div>
    </td>
    <td>
      <div class="ui progress" :class="getStatus ? getProcessBarStatus(getRAMStatus) : 'error'">
        <div class="bar" :style="{'width': `${getRAMStatus.toString()}%`}">
          {{ getStatus ? `${getRAMStatus.toString()}%` : '维护中' }}
        </div>
      </div>
    </td>
    <td>
      <div class="ui progress" :class="getStatus ? getProcessBarStatus(getHDDStatus) : 'error'">
        <div class="bar" :style="{'width': `${getHDDStatus.toString()}%`}">
          {{ getStatus ? `${getHDDStatus.toString()}%` : '维护中' }}
        </div>
      </div>
    </td>
    <td>
      <div class="ui progress" :class="getStatus ? getPacketLossStatus(server.ping_189) : 'error'">
        <div class="bar" style="width:100%">
          {{ getStatus ? `${server.ping_189}%` : '维护中' }}
        </div>
      </div>
    </td>
    <td>
      <div class="ui progress" :class="getStatus ? getPacketLossStatus(server.ping_10010) : 'error'">
        <div class="bar" style="width:100%">
          {{ getStatus ? `${server.ping_10010}%` : '维护中' }}
        </div>
      </div>
    </td>
    <td>
      <div class="ui progress" :class="getStatus ? getPacketLossStatus(server.ping_10086) : 'error'">
        <div class="bar" style="width:100%">
          {{ getStatus ? `${server.ping_10086}%` : '维护中' }}
        </div>
      </div>
    </td>
  </tr>
  <tr class="expandRow">
    <td colspan="16">
      <div :class="{collapsed}" :style="{'max-height': getStatus ? '' : '0'}">
        <div id="expand_mem">内存信息: {{
            getStatus ? `${expandRowByteConvert(server.memory_used * 1024)} / ${expandRowByteConvert(server.memory_total * 1024)}` : '–'
          }}
        </div>
        <div id="expand_swap">交换分区: {{
            getStatus ? `${expandRowByteConvert(server.swap_used * 1024)} / ${expandRowByteConvert(server.swap_total * 1024)}` : '–'
          }}
        </div>
        <div id="expand_ping">Ping CT/CU/CM: {{
            getStatus ? `${server.time_189}ms / ${server.time_10010}ms / ${server.time_10086}ms` : '–'
          }}
        </div>
        <div id="expand_loss">丢包 CT/CU/CM: {{
            getStatus ? `${server.ping_189}% / ${server.ping_10010}% / ${server.ping_10086}%` : '–'
          }}
        </div>
        <div id="expand_tupd">TCP/UDP/进程/线程: {{
            getStatus ? `${server.tcp_count} / ${server.udp_count} / ${server.process_count} / ${server.thread_count}` : '–'
          }}
        </div>
      </div>
    </td>
  </tr>
</template>

<script lang="ts">
import { defineComponent, ref, PropType } from 'vue';
import useStatus from './useStatus';

export default defineComponent({
  name: 'TableItem',
  props: {
    server: {
      type: Object as PropType<StatusItem | BoxItem>,
      default: () => ({})
    }
  },
  setup(props) {
    const collapsed = ref(true);
    const utils = useStatus(props);
    return {
      collapsed,
      ...utils
    };
  }
});
</script>

<style scoped>

tr.tableRow {
  background-color: rgba(249, 249, 249, .8);
  vertical-align: middle;
}

tr.expandRow td > div {
  overflow: hidden;
  transition: max-height .5s ease;
  max-height: 8em;
}

tr.expandRow td > div.collapsed {
  max-height: 0;
}

div.progress {
  display: inline-block;
  overflow: hidden;
  height: 25px;
  width: 50px;
  border-radius: 6px;
  margin-bottom: 0 !important;
}

div.progress div.bar {
  height: 25px;
  border-radius: 6px;
  font-size: .9rem;
  line-height: 25px;
  color: white;
}

tr td {
  color: #616366;
  font-weight: bold;
  border: none !important;
  white-space: nowrap;
}
</style>
