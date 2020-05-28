<template>
  <ListTable :table-config="tableConfig" :header-actions="headerActions" />
</template>

<script>
import ListTable from '@/components/ListTable'
import { toSafeLocalDateStr } from '@/utils/common'

export default {
  name: 'TaskHistory',
  components: {
    ListTable
  },
  props: {
    object: {
      type: Object,
      default: () => ({})
    }
  },
  data() {
    return {
      tableConfig: {
        url: `/api/v1/ops/adhoc-executions/?task=${this.object.id}`,
        columns: [
          'date_start', 'stat', 'ratio', 'is_finished', 'is_success', 'timedelta', 'adhoc_short_id', 'actions'
        ],
        columnsMeta: {
          date_start: {
            formatter: function(row) {
              return toSafeLocalDateStr(row.date_start)
            }
          },
          stat: {
            label: this.$t('ops.stat'),
            align: 'center',
            width: '80px',
            formatter: function(row) {
              const summary = <div>
                <span class='text-primary'>{row.stat.success}</span>/
                <span class='text-danger'>{row.stat.failed}</span>/
                <span>{row.stat.total}</span>
              </div>
              return summary
            }
          },
          ratio: {
            label: this.$t('ops.ratio'),
            align: 'center',
            width: '80px',
            formatter: function(row) {
              const ratio = (row.stat.success / row.stat.total) * 100
              if (ratio === 100) {
                return <span class='text-navy'>{ratio + '%'}</span>
              }
              return <span class='text-danger'>{ratio + '%'}</span>
            }
          },
          is_finished: {
            align: 'center',
            width: '200px',
            label: this.$t('ops.isFinished')
          },
          is_success: {
            align: 'center',
            width: '200px',
            label: this.$t('ops.isSuccess')
          },
          timedelta: {
            label: this.$t('ops.time'),
            formatter: function(row) {
              return row.timedelta.toFixed(2) + 's'
            }
          },
          adhoc_short_id: {
            label: this.$t('ops.version')
          },
          actions: {
            prop: 'id',
            formatterArgs: {
              hasEdit: false,
              hasDelete: false,
              hasUpdate: false,
              extraActions: [
                {
                  name: 'detail',
                  title: this.$t('ops.detail'),
                  type: 'primary',
                  callback: function({ cellValue, tableData }) {
                    return this.$router.push({ name: 'HistoryExecutionDetail', params: { id: cellValue }})
                  }
                }
              ]
            }
          }
        }
      },
      headerActions: {
        hasLeftActions: false,
        hasRightActions: false
      }
    }
  }
}
</script>

<style scoped>

</style>