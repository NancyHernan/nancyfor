# nancyfor
//
//  StickerSelectorView.swift
//  CollageBuilder


import SwiftUI

struct StickerSelectorView: View {
    
    @EnvironmentObject private var store: AppStore
    @Environment(\.dismiss) private var dismiss
    
    @State private var selectedSticker = StickersProvider.allStickers[0]
    
    var maxZPosition: Int
    
    private let allStickers = StickersProvider.allStickers
    private let columns = [
        GridItem(.flexible()),
        GridItem(.flexible()),
        GridItem(.flexible()),
        GridItem(.flexible()),
    ]
    
    var body: some View {
        GeometryReader { geo in
            VStack {
                topBar
                mainImage
                stickersSelector
                    .frame(height: geo.size.height * 0.6)
            }
        }
    }
    
    private var stickersSelector: some View {
        ScrollView {
            LazyVGrid(columns: columns, spacing: 20) {
                ForEach(allStickers) { sticker in
                    Button {
                        selectedSticker = sticker
                    } label: {
                        Image(uiImage: sticker.image)
                            .resizable()
                            .scaledToFit()
                    }
                    .buttonStyle(.plain)
                    .overlay {
                        let isSelected = selectedSticker.id == sticker.id
                        RoundedRectangle(cornerRadius: 10)
                            .strokeBorder(isSelected ? .green : .clear,
                                          lineWidth: 3)
                    }
                }
            }
        }
        .padding(.horizontal, 10)
    }
    
    private var mainImage: some View {
        Image(uiImage: selectedSticker.image)
            .resizable()
            .scaledToFit()
            .padding(30)
            .frame(maxHeight: .infinity)
    }
    
    private var topBar: some View {
        HStack {
            Button("Close") {
                dismiss()
            }
            Spacer()
            Button("Apply") {
                var sticker = selectedSticker
                sticker.zPosition = maxZPosition
                
                store.dispatch(.changeCollage(
                    .addSticker(sticker)
                ))
                dismiss()
            }
        }
        .padding(.horizontal, 20)
        .padding(.top, 10)
    }
}

struct StickerSelectorView_Previews: PreviewProvider {
    static var previews: some View {
        StickerSelectorView(maxZPosition: 10)
            .environmentObject(AppStore.preview)
    }
}
