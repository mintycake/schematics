<template>

  <div class="container">

    <div class="row"></div>

    <div class="row">
      <div>

  <b-button class="mt-3 mb-3" variant="primary" @click="addRect()"><i class="far fa-square mr-2"></i>  Add Rectangle</b-button>
  <b-button class="mt-3 mb-3" variant="secondary" @click="addCustom()"><i class="fas fa-draw-polygon mr-2"></i> Add Polygon</b-button>


        <b-carousel
          id="carousel-fade"
          style="text-shadow: 0px 0px 2px #000"
          controls
          no-animation
          img-width="1024"
          img-height="480"
        >
          <b-carousel-slide
            caption="First slide"
            img-src="https://picsum.photos/1024/480/?image=10"
            id="example"
          >
            <v-stage
              ref="stage"
              :config="stageSize"
              @mousedown="handleStageMouseDown"
              @touchstart="handleStageMouseDown"
              id="tooltip"
            >
              <v-layer ref="layer">
                <v-rect
                  v-for="item in rectangles"
                  :key="item.id"
                  :config="item"
                  @transformend="handleTransformEnd"
                />
                
                <v-line
                  v-for="item in custom"
                  :key="item.id"
                  :config="item"
                  @transformend="handleTransformEnd"
                />
                <v-transformer ref="transformer" />

                <b-tooltip target="tooltip" triggers="click">
                  <label class="m-3" labelfor="schematic-select">Link to:</label>
                  <select name id="schematic-select" value="schematic">
                    <option value="schematic1">Schematic 1</option>
                    <option value="schematic2">Schematic 2</option>
                    <option value="schematic3">Schematic 3</option>
                    <option value="schematic4">Schematic 4</option>
                  </select>

                  <b-button class="m-3" :variant="primary" @click="removeShape()">Remove region</b-button>
                </b-tooltip>
              </v-layer>
            </v-stage>
          </b-carousel-slide>

          <b-carousel-slide
            caption="Second Slide"
            img-src="https://picsum.photos/1024/480/?image=12"
          ></b-carousel-slide>
        </b-carousel>
      </div>
    </div>

    <div class="row"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      stageSize: {
        width: 1024,
        height: 480,
      },
      rectangles: [
        {
          rotation: 0,
          x: 10,
          y: 10,
          width: 100,
          height: 100,
          scaleX: 1,
          scaleY: 1,
          fill: "red",
          name: "rect1",
          draggable: true,
        },
        {
          rotation: 0,
          x: 150,
          y: 150,
          width: 100,
          height: 100,
          scaleX: 1,
          scaleY: 1,
          fill: "green",
          name: "rect2",
          draggable: true,
        },
      ],
      custom: [
      {
        points: [23, 20, 23, 160, 70, 93, 150, 109, 290, 139, 270, 93],
        fill: '#00D2FF',
        stroke: 'black',
        strokeWidth: 1,
        closed: true,
        draggable: true,
        enabledAnchors: ['top-left', 'top-right', 'bottom-left', 'bottom-right']
      }
      ],
      selectedShapeName: "",
    };
  },
  methods: {
    addRect(){
      let newRectangle = {
          rotation: 0,
          x: 10,
          y: 10,
          width: 100,
          height: 100,
          scaleX: 1,
          scaleY: 1,
          fill: "red",
          name: "rect1",
          draggable: true,
    }
    this.rectangles.push(newRectangle);
    },
    addCustom(){
      let newCustom = {
        points: [53, 20, 13, 160, 70, 93, 100, 109, 200, 139, 260, 83],
        fill: 'pink',
        stroke: 'black',
        strokeWidth: 1,
        closed: true,
        draggable: true,
    }
    this.custom.push(newCustom);
    },

    handleTransformEnd(e) {
      // shape is transformed, let us save new attrs back to the node
      // find element in our state
      const rect = this.rectangles.find(
        (r) => r.name === this.selectedShapeName
      );
      // update the state
      rect.x = e.target.x();
      rect.y = e.target.y();
      rect.rotation = e.target.rotation();
      rect.scaleX = e.target.scaleX();
      rect.scaleY = e.target.scaleY();

      // change fill
      rect.fill = Konva.Util.getRandomColor();
    },
    handleStageMouseDown(e) {
      // clicked on stage - clear selection
      if (e.target === e.target.getStage()) {
        this.selectedShapeName = "";
        this.updateTransformer();
        return;
      }

      // clicked on transformer - do nothing
      const clickedOnTransformer =
        e.target.getParent().className === "Transformer";
      if (clickedOnTransformer) {
        return;
      }

      // find clicked rect by its name
      const name = e.target.name();
      const rect = this.rectangles.find((r) => r.name === name);
      if (rect) {
        this.selectedShapeName = name;
      } else {
        this.selectedShapeName = "";
      }
      this.updateTransformer();
    },
    removeShape() {
      selectedShapeName.hide();
      layer.draw();
    },
    updateTransformer() {
      // here we need to manually attach or detach Transformer node
      const transformerNode = this.$refs.transformer.getNode();
      const stage = transformerNode.getStage();
      const { selectedShapeName } = this;

      const selectedNode = stage.findOne("." + selectedShapeName);
      // do nothing if selected node is already attached
      if (selectedNode === transformerNode.node()) {
        return;
      }
      if (selectedNode) {
        // attach to another node
        transformerNode.nodes([selectedNode]);
      } else {
        // remove transformer
        transformerNode.nodes([]);
      }
      transformerNode.getLayer().batchDraw();
    },
  },
};
</script>

<style>
</style>
