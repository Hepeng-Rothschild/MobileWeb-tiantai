<template>
  <div class="create_partner">
    <van-cell-group>
      <div class="mb17 border_top">
        <van-cell title="客户名称">
          <van-field
            v-model="savePartner.name"
            placeholder="请输入"
            input-align="right"
            style="border:0px;"
          />
        </van-cell>
        <van-cell
          title="客户性质"
          :value="savePartner.partnerTypeName?savePartner.partnerTypeName:'客户'"
          is-link
          @click="showProperty = true"
        ></van-cell>
        <van-popup v-model="showProperty" position="bottom" :style="{ height: '30%' }">
          <div
            v-for="(property,index) in partnerTypeArr"
            :key="index"
            @click="changePartnerType(property)"
          >{{property.Name}}</div>
        </van-popup>
        <van-cell
          title="所属类别"
          :value="savePartner.partnerClassName?savePartner.partnerClassName:'请选择'"
          is-link
          :class="savePartner.partnerClassName?'van-cell_true':'van-cell'"
          @click="showCategory = true"
        ></van-cell>
        <van-popup v-model="showCategory" position="bottom" :style="{ height: '30%' }">
          <div
            v-for="(category,index) in this.partnerClass"
            :key="index"
            @click="changePartnerClass(category)"
          >{{category.Name}}</div>
        </van-popup>
      </div>
      <div class="mb17 border_top">
        <van-cell
          title="分管部门 选填"
          :value="savePartner.saleDepartmentName?savePartner.saleDepartmentName:'请选择'"
          is-link
          @click="showDepartment = true"
        ></van-cell>
        <van-popup v-model="showDepartment" position="bottom" :style="{ height: '30%' }">
          <div
            v-for="(department,index) in saleDepartment"
            :key="index"
            @click="changeManageName(department)"
          >{{department.name}}</div>
        </van-popup>
        <van-cell
          title="分管人员"
          :value="savePartner.saleManName?savePartner.saleManName:'请选择'"
          is-link
          @click="showManages = true"
        ></van-cell>
        <van-popup v-model="showManages" position="bottom" :style="{ height: '30%' }">
          <div
            v-for="(manage,index) in saleMan"
            :key="index"
            @click="changeSalemanName(manage)"
          >{{manage.name}}</div>
        </van-popup>
      </div>
      <div class="mb17 border_top">
        <van-cell
          title="默认收款方式"
          :value="savePartner.saleSettleStyleName?savePartner.saleSettleStyleName:'请选择'"
          is-link
          to="/defaultPay"
        ></van-cell>
      </div>
      <div class="mb17 border_top">
        <van-cell title="联系人">
          <van-field
            v-model="savePartner.contact"
            placeholder="请输入"
            input-align="right"
            style="border:0px;"
          />
        </van-cell>
        <van-cell title="联系电话">
          <van-field
            v-model="savePartner.mobilePhone"
            placeholder="请输入"
            input-align="right"
            style="border:0px;"
          />
        </van-cell>
        <van-cell title="到货地址">
          <van-field
            v-model="savePartner.shipmentAddress"
            placeholder="请输入"
            input-align="right"
            style="border:0px;"
          />
        </van-cell>
      </div>
      <div class="border_top">
        <van-cell title="发货要求">
          <van-field
            v-model="savePartner.priuserdefnvc2"
            placeholder="请输入"
            input-align="right"
            style="border:0px;"
          />
        </van-cell>
        <van-cell title="到货要求">
          <van-field
            v-model="savePartner.priuserdefnvc5"
            placeholder="请输入"
            input-align="right"
            style="border:0px;"
          />
        </van-cell>
      </div>
    </van-cell-group>
    <div class="button">
      <van-button type="info" @click="inputMessage">保存</van-button>
    </div>
  </div>
</template>

<script>
import { setItem, getItem, remItem } from "../../utils/Storage.js";
import { mapState } from "vuex";
export default {
  data() {
    return {
      // 唯一识别号uuid
      code: this.uuidcode(),
      // 客户性质----固定内容
      partnerTypeArr: [
        { Code: "01", Name: "客户" },
        { Code: "00", Name: "供应商" },
        { Code: "02", Name: "客户/供应商" }
      ],
      // 所属类别----需要遍历展示的数据
      partnerClass: null,
      // 分管部门----需要遍历展示的数据
      saleDepartment: null,
      // 分管部门下的销售员----需要遍历展示的数据
      saleMan: null,
      // 以下是遮罩层的开启名称
      showCategory: false,
      showProperty: false,
      showDepartment: false,
      showManages: false,
      //  收款方式的代号，不能放在对象里，会报错
      saleSettleStyle: { Code: null },
      // 选择要储存的参数，把它们包装成一个大数组
      savePartner: {
        //客户名称
        name: null,
        // 联系人
        contact: null,
        // 电话
        mobilePhone: null,
        // 地址
        shipmentAddress: null,
        // 确定修改类型
        status: "1",
        // 客户性质
        partnerType: { Code: "01" },
        partnerTypeName: null,
        // 客户类别
        partnerClassName: null,
        partnerClassCode: { Code: null },
        // 分管部门
        saleDepartmentName: null,
        saleDepartmentCode: { Code: null },
        //分管人员
        saleManName: null,
        saleManCode: { Code: null },
        departmentCode: null,
        priuserdefnvc2: null, //发货要求
        priuserdefnvc5: null, // 发货备注
        // 默认收款方式----开始
        saleSettleStyleName: null,
        saleStartDate: null,
        saleSpaceMonth: null,
        saleCheckMonth: null,
        saleCheckDate: null
        // 默认收款方式----结束
      }
    };
  },
  beforeRouteLeave(to, from, next) {
    if (to.name == "defaultPay") {
      from.meta.keepAlive = true;
    } else {
      from.meta.keepAlive = false;
    }
    next();
  },
  watch: {
    savePartner: {
      handler(newVal, oldVal) {
        this.$store.commit("savePartners", this.savePartner);
      },
      deep: true
    }
  },
  computed: {
    ...mapState(["savePartners", "defaultPay"])
  },
  created() {
    this.changeCategory();
    this.changeManage();
    this.changeSaleman();
  },
  mounted() {
    this.selectDate();
  },
  methods: {
    changePartnerType(propertyNew) {
      this.savePartner.partnerTypeName = propertyNew.Name;
      this.savePartner.partnerType.Code = propertyNew.Code;
      this.showProperty = false;
    },
    // 选择类型
    changePartnerClass(categoryNew) {
      this.savePartner.partnerClassName = categoryNew.Name;
      this.savePartner.partnerClassCode.Code = categoryNew.Code;
      this.showCategory = false;
    },
    // 选择所属类别
    async changeCategory() {
      const { data } = await this.$Parse.Cloud.run("getPartnerClass");
      this.partnerClass = JSON.parse(data);
    },
    // 选择分管部门
    async changeManage() {
      const { data } = await this.$Parse.Cloud.run("department");
      this.saleDepartment = data;
    },
    changeManageName(departmentNew) {
      console.log(departmentNew, "123");

      this.savePartner.saleDepartmentName = departmentNew.name;
      this.savePartner.saleDepartmentCode.Code = departmentNew.code;
      this.savePartner.departmentCode = departmentNew.code;
      this.savePartner.saleManName = null;
      this.changeSaleman();
      this.showDepartment = false;
    },
    // 选择分管人员
    async changeSaleman() {
      // const { data } = await this.$Parse.Cloud.run("department");
      // // console.log(data,'==========')
      // let saleMan = []
      // if(this.savePartner.departmentCode){
      //   saleMan = data.find(v => v.code === this.savePartner.departmentCode).staffList
      // }else{
      //   data.forEach(v => saleMan =  saleMan.concat(v.staffList))
      // }
      // this.saleMan = saleMan
    },
    changeSalemanName(manageNew) {
      this.savePartner.saleManCode.Code = manageNew.code;
      this.savePartner.saleManName = manageNew.name;
      this.showManages = false;
    },
    // 编写UUID
    uuidcode() {
      let originStr = "xxxxx",
        originChar = "0123456789",
        len = originChar.length;
      return originStr.replace(/x/g, function(match) {
        return originChar.charAt(Math.floor(Math.random() * len));
      });
    },
    // 新增客户
    async createPartner() {
      const { data } = await this.$Parse.Cloud.run("createPartner", {
        Code: this.code,
        Name: this.savePartner.name,
        PartnerAddresDTOs: [
          {
            Contact: this.savePartner.contact,
            TelephoneNo: this.savePartner.mobilePhone,
            ShipmentAddress: this.savePartner.shipmentAddress
          }
        ],
        Contact: this.savePartner.contact,
        MobilePhone: this.savePartner.mobilePhone,
        ShipmentAddress: this.savePartner.shipmentAddress,
        PartnerType: this.savePartner.partnerType,
        PartnerClass: this.savePartner.partnerClassCode,
        SaleDepartment: this.savePartner.saleDepartmentCode,
        SaleMan: this.savePartner.saleManCode,
        Status: this.savePartner.status,
        DynamicPropertyKeys: [
          "priuserdefnvc1",
          "priuserdefnvc2",
          "priuserdefnvc5"
        ],
        DynamicPropertyValues: [
          "5",
          this.savePartner.priuserdefnvc2,
          this.savePartner.priuserdefnvc5
        ],
        SaleSettleStyle: this.saleSettleStyle,
        SaleStartDate: this.savePartner.saleStartDate || null,
        SaleSpaceMonth: this.savePartner.saleSpaceMonth || null,
        SaleCheckMonth: this.savePartner.saleCheckMonth || null,
        SaleCheckDate: this.savePartner.saleCheckDate || null
      });
      this.$store.state.savePartners = null;
      this.$store.state.defaultPay = {
        radio: null,
        saleCreditDays: null,
        aleStartDate: null,
        saleSpaceMonth: null,
        saleCheckMonth: null,
        saleCheckDate: null
      };
      // console.log(data);
    },
    // 判断页面中的内容进行提示
    inputMessage() {
      let inputPartner = this.savePartner;
      let payDate = this.$store.state.defaultPay;
      if (!inputPartner.name) {
        this.$toast.fail("客户名称不能为空");
        return;
      } else if (!inputPartner.partnerClassCode.Code) {
        this.$toast.fail("所属类别不能为空");
        return;
      } else if (!inputPartner.saleDepartmentCode.Code) {
        this.$toast.fail("分管部门不能为空");
        return;
      } else if (!inputPartner.saleManCode.Code) {
        this.$toast.fail("分管人员不能为空");
        return;
      } else if (!inputPartner.saleSettleStyleName) {
        this.$toast.fail("默认收款方式不能为空");
        return;
      } else if (!inputPartner.contact) {
        this.$toast.fail("联系人不能为空");
        return;
      } else if (!inputPartner.mobilePhone) {
        this.$toast.fail("联系电话不能为空");
        return;
      } else if (!inputPartner.shipmentAddress) {
        this.$toast.fail("到货地址不能为空");
        return;
      }
      // 以下代码有待测试
      else if (!payDate.saleCreditDays) {
        this.$toast.fail("销货x天内收款不能为空");
      } else if (!payDate.aleStartDate) {
        this.$toast.fail("账单起始日不能为空");
      } else if (!payDate.saleSpaceMonth) {
        this.$toast.fail("每x个月为账期不能为空");
      } else if (!payDate.saleCheckMonth) {
        this.$toast.fail("结算日期为对账后的x个月不能为空");
      } else if (!payDate.saleCheckDate) {
        this.$toast.fail("结算日期为对账后的x日不能为空");
      } else {
        this.createPartner();
        this.$router.push({ name: "partnerList" });
        this.$toast.success("新增客户成功");
      }
    },
    // 拿到vuex中存储的收款方式限定天数/收款方式名称
    selectDate() {
      let sale_Date = this.$store.state.defaultPay;
      let name = this.$store.state.defaultPay.radio;
      if (name) {
        this.savePartner.saleSettleStyleName = name;
      } else {
        return this.savePartner.saleSettleStyleName;
      }
      if (name == "限期收款") {
        this.saleSettleStyle.Code = "00";
      } else if (name == "全额订金") {
        this.saleSettleStyle.Code = "01";
      } else if (name == "全额现结") {
        this.saleSettleStyle.Code = "02";
      } else if (name == "月结") {
        this.saleSettleStyle.Code = "03";
      } else {
        this.saleSettleStyle.Code = "05";
      }
      this.savePartner.saleStartDate = sale_Date.aleStartDate;
      this.savePartner.saleSpaceMonth = sale_Date.saleSpaceMonth;
      this.savePartner.saleCheckMonth = sale_Date.saleCheckMonth;
      this.savePartner.saleCheckDate = sale_Date.saleCheckDate;
    }
  }
};
</script>

<style lang="less" scoped>
.create_partner {
  height: 100%;
  background-color: @bgColor_gray;
  .van-cell-group {
    padding-top: 14px;
    color: @fontColor_black;
    border-top: 1px solid @borderColor_gray;
    background-color: @bgColor_gray;

    // 底部弹框样式
    .van-popup {
      div {
        height: 40px;
        line-height: 40px;
        text-align: center;
        font-size: 17px;
      }
    }
    // 常规状态下单元格样式
    .van-cell {
      font-size: 17px;
      padding: 10px 15px;
      border-bottom: 1px solid @borderColor_gray;
      // 单元格内输入框样式
      .van-field {
        padding: 0px;
        padding-right: 20px;
      }
      .van-cell__title {
        color: @fontColor_black;
      }
    }
    .filed {
      border-top: 1px solid @borderColor_gray;
    }
  }
  .button {
    background-color: @bgColor_gray;
    padding: 27px 20px 19px 20px;
    .van-button {
      width: 100%;
      border-radius: 5px;
      text-align: center;
    }
  }
}
</style>
