# PlanLib docker image
FROM kclplanning/rosplan:latest

SHELL ["/bin/bash", "-c"]
WORKDIR /root/ws

# Clone plan_lib
RUN git clone https://github.com/ionut94/plan_library.git src/plan_library

RUN sudo apt update && sudo apt install python-pip -y && pip install -U PyYAML pandas

# Further dependencies
RUN apt install ros-melodic-rviz-visual-tools -qq -y &&\
	source devel/setup.bash &&\
    rosdep update &&\
    rosdep install --from-paths src/plan_library --ignore-src -q -r -y

# Patch rosplan for failing simulated actions. Thanks Stefan Bezrucav!
RUN cd src/rosplan &&\
	git apply ../plan_library/experiments/action_interface_patch.patch

RUN sed -i "s|int main(int argc, char \*\*argv) {|int main(int argc, char **argv) {\n                /\* initialize random seed: \*/\n                srand (time(NULL));|g" /root/ws/src/rosplan/rosplan_planning_system/src/ActionInterface/RPSimulatedActionInterface.cpp


# Build workspace
RUN catkin build --summarize --no-status

# Important to run this from the run_docker.sh script
ENTRYPOINT ["bash", "/root/ws/src/plan_library/experiments/run_experiment.sh"]

