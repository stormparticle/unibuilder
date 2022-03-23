# unibuilder

this builds unis on the Nitel network.
uses netinfo as source of truth.
still depends on netinfo to provide following:
-Core router (A-side, Z-side, PAA)
-NNI (A-side, Z-side, PAA)
-VLAN (A-side, Z-side, PAA)
-EVC ID (PAA EVC and customer EVC)

high level overview of functions.
1. check underlying NNIs on both A-side and Z-side
    - verify NNIs are up
        A-side:
            "show int tengige0/4/2/0"
        Z-side:
            "show int tengige0/6/6/6"

    - verify NNI descriptions line up
        A-side:
            name = "show int tengige0/4/2/0 description"
            if $name contains $NIT:
                return true
        Z-side:
            name = "show int tengige0/6/6/6 description"
            if $name contains $NIT:
                return true

    - verify if NNI/VLAN in use
        A-side:
            config_vlan = "show int tengige0/4/2/0.420"
            if $config_vlan contains 'interface not found'
                return False
        Z-side:
            config_vlan = "show int tengige0/6/6/6.69"
            if $config_vlan contains 'interface not found'
                return False

    - verify if EVC/p2p ID are already in use on the relevant routers
        A-side:
            config_vlan = "show l2vpn xconnect group 42069"
            if $config_vlan contains 'xconnect not found'
                return False
        Z-side:
            config_vlan = "show l2vpn xconnect group 42069"
            if $config_vlan contains 'xconnect not found'
                return False

1. assuming all pre-checks are good proceed with opening sessions to each core router (somewhere between 2-3 devices depending on build)
1. 