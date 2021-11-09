#
# Copyright 2021 Kien Minh
#
# SPDX-License-Identifier:      GPL-2.0+
#

obj-y += imx8mm_maaxboard.o

ifdef CONFIG_SPL_BUILD
obj-y += spl.o
obj-$(CONFIG_IMX8M_DDR4) += ddr4_timing.o
endif
