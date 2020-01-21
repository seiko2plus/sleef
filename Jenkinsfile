stage "Build"

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
  stash name: "buildDir", includes: "**"
}

stage "Test"

node('x86') {
  unstash "buildDir"
  sh '''
  set -ev
  cd build
  export OMP_WAIT_POLICY=passive
  export CTEST_OUTPUT_ON_FAILURE=TRUE
  ctest -j 4
  make install
  '''
}
