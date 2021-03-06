FROM debian:jessie

# https://www.google.com/linuxrepositories/
ENV GPG_KEYS \
# pub   rsa4096 2016-04-12 [SC]
#       EB4C 1BFD 4F04 2F6D DDCC  EC91 7721 F63B D38B 4796
# uid           [ unknown] Google Inc. (Linux Packages Signing Authority) <linux-packages-keymaster@google.com>
# sub   rsa4096 2016-04-12 [S] [expires: 2019-04-12]
		EB4C1BFD4F042F6DDDCCEC917721F63BD38B4796
# pub   dsa1024 2007-03-08 [SC]
#       4CCA 1EAF 950C EE4A B839  76DC A040 830F 7FAC 5991
# uid           [ unknown] Google, Inc. Linux Package Signing Key <linux-packages-keymaster@google.com>
# sub   elg2048 2007-03-08 [E]
#		4CCA1EAF950CEE4AB83976DCA040830F7FAC5991

RUN set -ex; \
	\
	toRemove=''; \
	if ! command -v gpg; then \
		apt-get update; \
		toRemove="$toRemove gnupg2"; \
		apt-get install -y --no-install-recommends gnupg2; \
		if ! command -v dirmngr; then \
			toRemove="$toRemove dirmngr"; \
			apt-get install -y --no-install-recommends dirmngr; \
		fi; \
		rm -rf /var/lib/apt/lists/*; \
	fi; \
	\
	export GNUPGHOME="$(mktemp -d)"; \
	for key in $GPG_KEYS; do \
		gpg --keyserver ha.pool.sks-keyservers.net --recv-keys "$key"; \
	done; \
# only stretch+ supports --armor in trusted.gpg.asc
#	gpg --export --armor $GPG_KEYS | tee /etc/apt/trusted.gpg.d/google.gpg.asc; \
	gpg --export $GPG_KEYS > /etc/apt/trusted.gpg.d/google.gpg; \
	rm -rf "$GNUPGHOME"; \
	\
	if [ -n "$toRemove" ]; then \
		apt-get purge -y --auto-remove $toRemove; \
	fi; \
	\
	echo "deb [arch=$(dpkg --print-architecture)] http://dl.google.com/linux/musicmanager/deb stable main" > /etc/apt/sources.list.d/google-musicmanager.list

ENV GMM_VERSION 1.0.243.1116-r0

RUN set -ex; \
	apt-get update; \
	apt-get install -yV google-musicmanager-beta="$GMM_VERSION"; \
	rm -rf /var/lib/apt/lists/*
