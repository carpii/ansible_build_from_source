## ansible_build_from_source.yml

A generic ansible task to compile a binary from a remote source archive

Aims to be robust and idempotent, whilst also ensuring a binary is rebuild if any aspect of the build configuration changes (version, configure line etc)

The task file is platform agnostic, but the examples just happen to target CentOS 7

Do what you want with it, but contributions very welcome

### Basic operation:

- Downloads source archive (only if not already present)
- Extracts the archive (only if not already extracted)
- Creates or updates `config.ansible` in source dir (to record current build procedure)
- Runs full compile and install steps (only if required)

### Example Usage:

````
# build php from source
- import_tasks: build_from_source.yml
  vars:
    compile: 
      label: 'PHP'
      source_version: '7.4.13'
      source_base_name: 'php-[VERSION]'
      source_archive_extension: 'tar.gz'
      source_base_url: 'https://www.php.net/distributions'
      pre_configure: 
        - "./buildconf --force"
      configure: 
        - "./configure --with-config-file-path=/etc --sysconfdir=/etc --with-config-file-scan-dir=/etc/php.d --with-zlib --with-curl --with-zlib-dir=/usr/local/lib --with-pear=/usr/share/pear --enable-mbstring=shared --enable-inline-optimization --enable-sockets --enable-mbstring=shared --disable-ipv6 --disable-dom --without-gdbm --with-openssl --with-pdo-mysql --prefix=/usr/local --enable-opcache"
      post_configure: 
        - "make"
        - "make install"
````

### var definitions:

`label (required)` 
   
   arbitrary text label - only used for display purposes in ansible output 

`source_version (required)`

   used to build remote archive url, name source directory,  determine if version has changed since last compile

`source_base_name (required)`

   used to build remote archive url, to name source directory once archive is extracted

`source_archive_extension (required)`

   used to build remote archive url (any extension supported by `ansible.builtin.unarchive`)

`source_base_url (required)`

   url prefix used to build remote tar url 

`pre_configure (optional)`

   list of commands to execute before configuring 
    
`configure (optional)`

   list of one or more commands to execute to configure source (usually just the `./configure` line)
    
`post_configure (optional)`

   list of commands to build source, usually `make`, `make install` etc
