SG bastion
  ingress
    TCP 22    from  0.0.0.0/0     # ssh to bastion
  egress
    TCP 22    to    SUBNET        # ssh jump to subnet hosts
    UDP 53    to    0.0.0.0/0     # apt resolve repos domain names
    TCP 80    to    0.0.0.0/0     # apt updates
    TCP 443   to    0.0.0.0/0     # apt updates

SG monitoring
  ingress
    TCP 22    from  SG bastion    # accept ssh jumps from bastion
    TCP 5601  from  0.0.0.0/0     # kibana GUI
    ANY --    from  SG internal   # communication with internal hosts
  egress
    UDP 53    to    0.0.0.0/0     # apt resolve repos domain names
    TCP 80    to    0.0.0.0/0     # apt updates
    TCP 443   to    0.0.0.0/0     # apt updates
    ANY --    to    SG internal   # communication with internal hosts

SG internal
  ingress
    TCP 22    from  SG bastion    # accept ssh jumps from bastion
    UDP 53    from  SUBNET        # dns zone transfer : root, tld, authoritative, recursive
    TCP 53    from  SUBNET        # dns requests : root, tld, authoritative, recursive
    ANY --    from  SG monitoring # monitoring
  egress
    UDP 53    to    SUBNET        # dns zone transfer : root, tld, authoritative, recursive
    TCP 53    to    SUBNET        # dns requests : root, tld, authoritative, recursive
    UDP 53    to    0.0.0.0/0     # apt resolve repos domain names
    TCP 80    to    0.0.0.0/0     # apt updates
    TCP 443   to    0.0.0.0/0     # apt updates
    ANY --    to    SG monitoring # monitoring