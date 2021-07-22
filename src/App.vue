<template>
  <div class="wrapper">
    <div class="node-wrapper">
      <div
        class="level level-top"
        @drop="onDrop($event, node)"
        @dragenter.prevent
        @dragover.prevent
      >
        <div
          v-for="node in nodes"
          :key="node.NodeID"
          class="node"
          :class="node.isLeftChild ? 'node-child' : 'node-parent'"
          draggable="true"
          @dragstart="onDrag($event, node)"
        >
          {{ node.nodeLabel }}

          <div class="connector">
            <div
              class="drop-zone"
              @drop="onDrop($event, node)"
              @dragenter.prevent
              @dragover.prevent
            ></div>
          </div>
        </div>
      </div>
      <div class="level">
        <div class="data-output">Node ID</div>
        <div v-for="node in nodes" :key="node.NodeID" class="data-output"
          >{{ node.NodeID }}
        </div>
      </div>
      <div class="level">
        <div class="data-output">Parent ID</div>
        <div v-for="node in nodes" :key="node.NodeID" class="data-output"
          >{{ node.parentId }}
        </div>
      </div>
      <div class="level">
        <div class="data-output">Left Child</div>
        <div v-for="node in nodes" :key="node.NodeID" class="data-output"
          >{{ node.leftChildId }}
        </div>
      </div>
      <div class="level">
        <div class="data-output">Right Child</div>
        <div v-for="node in nodes" :key="node.NodeID" class="data-output"
          >{{ node.hasBothChild }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      dragFlag: 0,
      dropFlag: 0,
      newNodeID: 0,
      newParentId: 0,
      newNodeLabel: '',
      isLeftChild: false,
      hasBothChild: false,
      hasLeftChild: false,
      leftChildId: 0,
      nodes: [
        {
          NodeID: 10,
          parentId: 0,
          nodeLabel: 'Viceroy',
          isLeftChild: false,
          hasBothChild: false,
          hasLeftChild: false,
          leftChildId: 0,
        },
        {
          NodeID: 50,
          parentId: 1,
          nodeLabel: 'Left child',
          isLeftChild: false,
          hasBothChild: false,
          hasLeftChild: false,
          leftChildId: 0,
        },
        {
          NodeID: 60,
          parentId: 2,
          nodeLabel: 'Right Child',
          isLeftChild: false,
          hasBothChild: false,
          hasLeftChild: false,
          leftChildId: 0,
        },
        {
          NodeID: 70,
          parentId: 3,
          nodeLabel: 'New Child',
          isLeftChild: false,
          hasBothChild: false,
          hasLeftChild: false,
          leftChildId: 0,
        },
      ],
    }
  },
  methods: {
    addNode() {
      const addedNode = {
        NodeID: parseInt(this.newNodeID),
        parentId: parseInt(this.newParentId),
        nodeLabel: this.newNodeLabel,
        isLeftChild: this.isLeftChild,
        hasBothChild: false,
        hasLeftChild: false,
      }
      this.nodes.push(addedNode)
      this.newNodeID = 0
      this.newNodeLabel = ''
      this.isLeftChild = false
      this.hasBothChild = false
      this.hasLeftChild = false
    },
    onDrag(event, node) {
      event.dataTransfer.dropEffect = 'move'
      event.dataTransfer.effectAllowed = 'move'
      event.dataTransfer.setData('clickedNode', node.NodeID)
      event.dataTransfer.setData('leftChildNode', node.leftChildId)
      event.dataTransfer.setData('nodeParentId', node.parentId)
      const leftChildNode = parseInt(
        event.dataTransfer.getData('leftChildNode')
      )
      const nodeParentId = parseInt(event.dataTransfer.getData('nodeParentId'))
      console.log(leftChildNode)
      console.log(nodeParentId)

      if (node.hasLeftChild) {
        for (let i = 0; i < this.nodes.length; i++) {
          console.log(this.nodes[i].leftChildId)
          if (this.nodes[i].NodeID == leftChildNode) {
            this.nodes[i].parentId = nodeParentId
            console.log('what')
            node.isLeftChild = false
            node.hasLeftChild = false
            node.hasBothChild = false
            node.leftChildId = 0
            console.log('Now has no child')
            this.dragFlag = 0
            this.count++
          }
        }
      }
    },

    onDrop(event, node) {
      const clickedNode = parseInt(event.dataTransfer.getData('clickedNode'))
      const newnode = this.nodes.find((node) => node.NodeID === clickedNode)
      for (let i = 0; i < this.nodes.length; i++) {
        if (
          this.nodes[i].parentId === node.parentId &&
          newnode.NodeID !== node.NodeID
        ) {
          if (!this.nodes[i].hasLeftChild) {
            this.nodes[i].hasLeftChild = true
            this.dropFlag = 0
            this.count++
          } else if (!this.nodes[i].hasBothChild) {
            this.nodes[i].hasBothChild = true
            this.dropFlag = 1
          } else if (this.nodes[i].hasBothChild && this.nodes[i].hasLeftChild) {
            this.dropFlag = 2
          }
        }
      }
      if (this.dropFlag === 0 || this.dropFlag === 1) {
        newnode.parentId = node.NodeID
        if (this.dropFlag === 0) {
          newnode.NodeID = parseInt(newnode.parentId + '1')
          node.leftChildId = newnode.NodeID
        } else if (this.dropFlag === 1) {
          newnode.NodeID = parseInt(newnode.parentId + '2')
        } else {
          this.dropFlag = 2
        }
      } else {
        console.log('outside')
      }
    },
  },
  computed: {},
}
</script>

<style lang="scss">
html,
body {
  font-family: 'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande',
    'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
}
form {
  margin-top: 100px;
}
.wrapper {
  margin: 50px auto;
  width: 90vw;
}
.node-wrapper {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr;
  align-items: center;
  height: 45vw;
  width: 95vw;
}
.level {
  height: 100%;
  width: 100%;
  justify-content: center;
  align-items: center;
  display: block;
}
.node {
  position: relative;
  margin: 50px 0 0 50px;
  height: 30px;
  width: 150px;
  min-height: 32px;
  border-radius: 3px;
  box-shadow: 1px 1px 5px 2px rgb(238, 238, 238);
  color: rgb(83, 83, 83);
  text-align: center;
  line-height: 32px;
  text-transform: uppercase;
  font-size: 16px;
  font-weight: 500;
  cursor: pointer;
  background-color: lightblue;
}
.node-parent {
  position: relative;
}
.node-parent::before {
  content: '';
  position: absolute;
  display: block;
  width: 25px;
  height: 15px;
  border-bottom: 2px solid lightgray;
  left: 100%;
}
.node-child {
  position: relative;
}
.node-child::before {
  content: '';
  position: absolute;
  display: block;
  width: 20%;
  height: 15px;
  border-bottom: 2px solid lightgray;
  right: 100%;
}
.connector {
  position: absolute;
  display: block;
  width: 12px;
  height: 47%;
  border-bottom: 2px solid lightgray;
  right: -43px;
  top: 0px;
}
.drop-zone {
  position: absolute;
  display: block;
  border: 2px solid lightgray;
  border-radius: 50%;
  min-height: 20px;
  min-width: 20px;
  right: -6px;
  top: 3px;
}
.data-output {
  text-align: center;
  padding: 32px;
}
.level-top {
  margin-top: 120px;
}
</style>