from scapy.all import rdpcap, IP, TCP, UDP, Raw

PCAP_FILE = "sample_capture.pcap"


def protocol_name(packet):
    if TCP in packet:
        return "TCP"
    elif UDP in packet:
        return "UDP"
    else:
        return "Other"


def analyze_packets():
    try:
        packets = rdpcap(PCAP_FILE)

        print("=" * 70)
        print(f"Loaded {len(packets)} packets")
        print("=" * 70)

        for i, packet in enumerate(packets, start=1):

            if IP in packet:
                print(f"\nPacket #{i}")
                print("-" * 50)

                print(f"Source IP      : {packet[IP].src}")
                print(f"Destination IP : {packet[IP].dst}")
                print(f"Protocol       : {protocol_name(packet)}")
                print(f"Packet Length  : {len(packet)} bytes")

                if TCP in packet:
                    print(f"Source Port    : {packet[TCP].sport}")
                    print(f"Destination Port: {packet[TCP].dport}")

                elif UDP in packet:
                    print(f"Source Port    : {packet[UDP].sport}")
                    print(f"Destination Port: {packet[UDP].dport}")

                if Raw in packet:
                    payload = bytes(packet[Raw].load)

                    try:
                        preview = payload.decode(errors="ignore")
                    except Exception:
                        preview = str(payload)

                    print(f"Payload Preview: {preview[:80]}")

                print("-" * 50)

    except FileNotFoundError:
        print("sample_capture.pcap was not found.")
    except Exception as e:
        print(f"Error: {e}")


if __name__ == "__main__":
    analyze_packets()