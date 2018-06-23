cmake_minimum_required(VERSION 2.8.3)

include(cmake/HunterGate.cmake)
HunterGate(
  URL "https://github.com/ruslo/hunter/archive/v0.22.16.tar.gz"
  SHA1 "84153076a3cebf4869c904fa5c93ea309386b583"
)

project(pluginlib-root)  # dummy project for HunterGate
add_subdirectory(pluginlib)
