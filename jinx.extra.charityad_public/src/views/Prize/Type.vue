<template>
  <div class="jinx-panel">
    <el-breadcrumb separator="/">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>获奖查询</el-breadcrumb-item>
    </el-breadcrumb>

    <div class="jinx-types">
      <div class="type" v-for="(item, index) in $WorksTypeCode" :key="'WorksTypeCode' + index"><div :class="{ active: type === item.code * 1 }" @click="handleTypeClick(item.code * 1)" v-text="item.value"></div></div>
    </div>

    <el-tabs v-model="tab_active">
      <el-tab-pane :label="item.value" :name="item.code + ''" v-for="(item, index) in $WorksGroupCode" :key="'WorksGroupCode' + index">
        <el-card shadow="never" v-for="(pitem, pindex) in PrizeList" :key="'prize' + pindex">
          <div slot="header">
            <span v-text="pitem"></span>
          </div>
          <el-table :data="Data.group[index].prize[pindex].list" stripe style="width: 100%" @row-dblclick="handleRowDbclick">
            <el-table-column type="index" width="50"> </el-table-column>
            <el-table-column prop="wno" label="作品编号" width="120"> </el-table-column>
            <el-table-column prop="worksName" label="作品名称"> </el-table-column>
            <el-table-column prop="gameType" label="参赛组别" width="120"> </el-table-column>
            <el-table-column prop="worksSeries" label="作品主题"> </el-table-column>
            <el-table-column prop="worksType" label="作品类别" width="120"> </el-table-column>
            <!--<el-table-column prop="scoreTotal" label="得分" width="120"> </el-table-column>-->
            <el-table-column label="操作" width="180">
              <template slot-scope="scope">
                <el-button @click="handleView(scope.row)" type="text" size="small">查看</el-button>
              </template>
            </el-table-column>
          </el-table>
        </el-card>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
import qs from "qs";

export default {
  data: function() {
    return {
      tab_active: "0",
      type: 1,
      PrizeList: ["一等奖", "二等奖", "三等奖", "优秀奖"],
      Data: {
        group: [
          {
            prize: [{ list: [] }, { list: [] }, { list: [] }, { list: [] }]
          },
          {
            prize: [{ list: [] }, { list: [] }, { list: [] }, { list: [] }]
          },
          {
            prize: [{ list: [] }, { list: [] }, { list: [] }, { list: [] }]
          },
          {
            prize: [{ list: [] }, { list: [] }, { list: [] }, { list: [] }]
          }
        ]
      }
    };
  },
  mounted: function() {
    this.getRankList();
  },
  methods: {
    handleTypeClick(type) {
      this.type = type;
      this.getRankList();
    },
    getRankList: function() {
      let that = this;
      this.Data = {
        group: [
          {
            prize: [{ list: [] }, { list: [] }, { list: [] }, { list: [] }]
          },
          {
            prize: [{ list: [] }, { list: [] }, { list: [] }, { list: [] }]
          },
          {
            prize: [{ list: [] }, { list: [] }, { list: [] }, { list: [] }]
          },
          {
            prize: [{ list: [] }, { list: [] }, { list: [] }, { list: [] }]
          }
        ]
      };
      const loading = this.$loading({
        lock: true,
        text: "Loading",
        spinner: "el-icon-loading",
        background: "rgba(0, 0, 0, 0.7)"
      });
      this.axios
        .post("/api/gameWorksRank/getRankByMap", qs.stringify({ worksType: this.type }))
        .then(function(response) {
          console.log(response);
          if (response && response.data.code == "0") {
            let data = response.data.data;
            data.forEach(p => {
              let game_type = that.$WorksGroupCode.find(x => x.code == p.gameType);
              p.gameType = game_type == null ? "" : game_type.value;
              let series = that.$WorksSeriesCode.find(x => x.code == p.worksSeries);
              p.worksSeries = series == null ? "" : series.value;
              let type = that.$WorksTypeCode.find(x => x.code == p.worksType);
              p.worksType = type == null ? "" : type.value;
              let source = that.$MaterialSurceCode.find(x => x.code == p.materialSurce);
              p.materialSurce = source == null ? "" : source.value;
            });

            let game_type0 = that.getGameTypeByCode("0");
            that.Data.group[0].prize[0].list = data.filter(p => p.gameType === game_type0 && p.prize === 1);
            that.Data.group[0].prize[1].list = data.filter(p => p.gameType === game_type0 && p.prize === 2);
            that.Data.group[0].prize[2].list = data.filter(p => p.gameType === game_type0 && p.prize === 3);
            that.Data.group[0].prize[3].list = data.filter(p => p.gameType === game_type0 && p.prize === 4);

            let game_type1 = that.getGameTypeByCode("1");
            that.Data.group[1].prize[0].list = data.filter(p => p.gameType === game_type1 && p.prize === 1);
            that.Data.group[1].prize[1].list = data.filter(p => p.gameType === game_type1 && p.prize === 2);
            that.Data.group[1].prize[2].list = data.filter(p => p.gameType === game_type1 && p.prize === 3);
            that.Data.group[1].prize[3].list = data.filter(p => p.gameType === game_type1 && p.prize === 4);

            let game_type2 = that.getGameTypeByCode("2");
            that.Data.group[2].prize[0].list = data.filter(p => p.gameType === game_type2 && p.prize === 1);
            that.Data.group[2].prize[1].list = data.filter(p => p.gameType === game_type2 && p.prize === 2);
            that.Data.group[2].prize[2].list = data.filter(p => p.gameType === game_type2 && p.prize === 3);
            that.Data.group[2].prize[3].list = data.filter(p => p.gameType === game_type2 && p.prize === 4);

            let game_type4 = that.getGameTypeByCode("4");
            that.Data.group[3].prize[0].list = data.filter(p => p.gameType === game_type4 && p.prize === 1);
            that.Data.group[3].prize[1].list = data.filter(p => p.gameType === game_type4 && p.prize === 2);
            that.Data.group[3].prize[2].list = data.filter(p => p.gameType === game_type4 && p.prize === 3);
            that.Data.group[3].prize[3].list = data.filter(p => p.gameType === game_type4 && p.prize === 4);
          } else {
            that.$message({
              showClose: true,
              message: response.data.msg,
              type: "warning"
            });
          }
          loading.close();
        })
        .catch(function(err) {
          console.log(err);
          loading.close();
          that.$message({
            showClose: true,
            message: "获奖结果查询失败",
            type: "warning"
          });
        });
    },
    getGameTypeByCode(code) {
      let game_type = this.$WorksGroupCode.find(x => x.code == code);
      return game_type == null ? code : game_type.value;
    },
    handleView: function(data) {
      this.$router.push({
        path: "/prize/work",
        query: {
          wid: data.wid
        }
      });
    },
    handleRowDbclick: function(row, column, event) {
      this.$router.push({
        path: "/prize/work",
        query: {
          wid: row.wid
        }
      });
    }
  }
};
</script>

<style lang="less" scoped>
.jinx-panel {
  width: @typical-width;
  margin: 30px auto 0 auto;
  box-sizing: border-box;
}

.jinx-types {
  display: flex;
  display: -webkit-flex;
  justify-content: space-between;

  .type {
    flex: 1;
    padding: 20px;

    div {
      border: 1px solid @primary-color;
      padding: 20px;
      text-align: center;
      cursor: pointer;
      transition: background 0.3s, color 0.3s, border-color 0.3s;
      border-radius: 7px;
    }

    div.active {
      background: @primary-color;
      border-color: @primary-color;
      color: #fefdfc;
    }
  }
}

/deep/ .el-card {
  margin-top: 20px;
}
</style>
