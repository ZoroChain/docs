How To Build Zoro on OracleLinux 7 or Centos7
=============================================

1)  Installing dotnet SDK.

In order to install required dependencies, please make sure following
repos are enabled: [ol7\_latest] name=Oracle Linux
$releasever Latest ($basearch)
baseurl=https://yum.oracle.com/repo/OracleLinux/OL7/latest/\$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle gpgcheck=1 enabled=1

[ol7\_optional\_latest] name=Oracle Linux
$releasever Optional Latest ($basearch)
baseurl=https://yum.oracle.com/repo/OracleLinux/OL7/optional/latest/\$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle gpgcheck=1 enabled=1

[ol7\_software\_collections] name=Software Collection Library release
3.0 packages for Oracle Linux 7 (x86\_64)
baseurl=https://yum.oracle.com/repo/OracleLinux/OL7/SoftwareCollections/x86\_64/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle gpgcheck=1 enabled=1

[ol7\_developer] name=Oracle Linux
$releasever Development Packages ($basearch)
baseurl=https://yum.oracle.com/repo/OracleLinux/OL7/developer/\$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle gpgcheck=1 enabled=1

[ol7\_developer\_EPEL] name=Oracle Linux
$releasever Development Packages ($basearch)
baseurl=https://yum.oracle.com/repo/OracleLinux/OL7/developer\_EPEL/\$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle gpgcheck=1 enabled=1

Then run below command to install dotnet-sdk. yum install
rh-dotnet20-dotnet-sdk-2.0 -y

2)  Installing Mono packages. rpm --import
    "https://keyserver.ubuntu.com/pks/lookup?op=get&search=0x3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF"
    su -c 'curl
    https://download.mono-project.com/repo/centos7-stable.repo | tee
    /etc/yum.repos.d/mono-centos7-stable.repo' yum install mono-devel

3)  Build Zoro. git clone https://github.com/ZoroChain/Zoro cd Zoro
    dotnet build


