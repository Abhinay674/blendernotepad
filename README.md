loaderGLTF.load(
  `${process.env.REACT_APP_VALIDATOR}/static/` + filename + "_" + this.state.templateTypeRadio + fileFormat,
  gltf => {
    console.log('start of loader');
    var object = gltf.scene;

    console.log("object in loader", object);

    object.rotation.y = 1.57;
    object.updateMatrixWorld();

    // Loop through each child of the glTF scene
    object.traverse(child => {
      if (child.isMesh) {
        var box = new THREE.Box3().setFromObject(child);
        var size = box.getSize().length();
        var center = box.getCenter();

        child.position.x += (child.position.x - center.x);
        child.position.y += (child.position.y - center.y);
        child.position.z += (child.position.z - center.z);

        this.controls.maxDistance = size * 1;
        this.camera.position.copy(center);
        this.camera.position.x += size / 2.7;
        this.camera.position.y += size / 5.0;
        this.camera.position.z += size / 0.80;
        this.camera.near = size / 10;
        this.camera.far = size * 100;
        this.camera.updateProjectionMatrix();
        this.camera.lookAt(center);

        this.controls.update();
      }
    });

    object.traverse(child => {
      if (child.isMesh) {
        this.scene.add(child);
      }
    });

    console.log('end of loader');
  },
  undefined,
  error => {
    console.error(error);
  }
);
