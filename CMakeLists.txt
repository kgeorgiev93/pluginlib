cmake_minimum_required(VERSION 2.8.3)

include(cmake/HunterGate.cmake)
HunterGate(
    URL "https://github.com/ruslo/hunter/archive/v0.23.197.tar.gz"
    SHA1 "f494a08bc9bb489527be1240d223d3ff69ece322"
)

project(pluginlib)
add_subdirectory(pluginlib)
