```diff
diff --git a/config/arm/armv8_machine.py b/config/arm/armv8_machine.py
index 1f689d9a83..58b287e6bd 100755
--- a/config/arm/armv8_machine.py
+++ b/config/arm/armv8_machine.py
@@ -3,10 +3,11 @@
 # Copyright(c) 2017 Cavium, Inc

 ident = []
-fname = '/sys/devices/system/cpu/cpu0/regs/identification/midr_el1'
-with open(fname) as f:
-    content = f.read()
+# fname = '/sys/devices/system/cpu/cpu0/regs/identification/midr_el1'
+# with open(fname) as f:
+#     content = f.read()

+content = '0x00000000481fd010'
 midr_el1 = (int(content.rstrip('\n'), 16))

 ident.append(hex((midr_el1 >> 24) & 0xFF))  # Implementer
```
