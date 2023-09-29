# references
https://www.youtube.com/watch?v=xQeGrgQJJDc
https://www.youtube.com/watch?v=gCJkR8Bs31Q
https://www.logicworks.com/blog/2017/12/common-mistakes-misconceptions-auto-scaling-aws/

# Concepts
1. Upfront signification investment on Configuration & Automation:
    building a template of application config., resources to automate the scale-in scale out needs.
    Use cloudformation, puppet, chef, AMI's

2. Load based scaling in/out + Achieving HA goals (via redundancy) during failures.

3. scaling down below reasonable limits will lead to high risk of downtime in case of spiky unplanned traffic

4. keep in consideration cloud hourly billing cycles and auto scale schedule should be in sync

5. set limits to upscale and downscale(atleast two nodes for HA considering commodity h/w based vm's)

6. lifecycle hooks can be attached on need basis

7. healthchecks:ec2 and ebs level, there is configurable grace b/w between two consecutive health checks

8. types of scaling: dynamic(demand based), predictive(ml based), scheduled

9. step scaling: where as per demand progression, scaling is done in phased manner

10. cooldowns: used to configure some time in b\w two consecutive rounds of scaling/terminating for letting metrics to refresh before second round.

# how to debug
https://www.youtube.com/watch?v=Uy_2l6gUdnc
