基于此篇文章编写 https://juejin.im/post/5d7f80796fb9a06b24434d4e?tdsourcetag=s_pcqq_aiomsg

    <virtualList :data="list">
        <template slot-scope="scope">
            <div class="list_item">{{ scope.row.name }}</div>
        </template>
    </virtualList>

props:

data: 数据

itemClass: 列表项的class

存在问题 滚动条超过一定长度时 直接拖动滚动条 列表无法正常渲染