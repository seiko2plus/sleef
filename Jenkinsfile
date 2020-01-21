
stage('Build') {
  parallel 'gcc': {
    node('x86') {
      checkout scm
      sh '''
      set -ev
      echo "Building on" `hostname`
      pwd
      export CC=gcc
      rm -rf build
      mkdir build
      cd build
      cmake -DCMAKE_INSTALL_PREFIX=../install -DSLEEF_SHOW_CONFIG=1 -DENFORCE_TESTER3=TRUE -DBUILD_QUAD=TRUE ..
      make -j 4 all
      '''
      stash name: "buildDir-gcc", includes: "**"
    }
  }, 'icc': {
    node('icc') {
      export PATH=$PATH:/opt/intel/compilers_and_libraries/linux/bin/intel64
      export LD_LIBRARY_PATH=/opt/intel/compilers_and_libraries/linux/lib/intel64
      checkout scm
      sh '''
      set -ev
      echo "Building with Intel Compiler on" `hostname`
      pwd
      export CC=icc
      rm -rf build
      mkdir build
      cd build
      cmake -DCMAKE_INSTALL_PREFIX=../install -DSLEEF_SHOW_CONFIG=1 -DENFORCE_TESTER3=TRUE -DBUILD_QUAD=TRUE -DBUILD_SHARED_LIBS=FALSE ..
      make -j 4 all
      '''
      stash name: "buildDir-icc", includes: "**"
    }
  }
}

stage('Test') {
  parallel 'gcc': {
    node('x86') {
      unstash "buildDir-gcc"
      sh '''
      set -ev
      cd build
      export OMP_WAIT_POLICY=passive
      export CTEST_OUTPUT_ON_FAILURE=TRUE
      ctest -j 4
      make install
      '''
    }
  }, 'icc': {
    node('x86') {
      unstash "buildDir-icc"
      sh '''
      set -ev
      cd build
      export OMP_WAIT_POLICY=passive
      export CTEST_OUTPUT_ON_FAILURE=TRUE
      ctest -j 4
      make install
      '''
    }
  }
}
